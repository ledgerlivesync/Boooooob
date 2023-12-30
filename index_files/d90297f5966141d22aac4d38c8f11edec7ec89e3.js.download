// Object.assign polyfill
//
if (!Object.assign) {
    Object.defineProperty(Object, 'assign', {
        enumerable: false,
        configurable: true,
        writable: true,
        value: function(target, firstSource) {
            'use strict';
            if (target === undefined || target === null) {
                throw new TypeError('Cannot convert first argument to object');
            }

            var to = Object(target);
            for (var i = 1; i < arguments.length; i++) {
                var nextSource = arguments[i];
                if (nextSource === undefined || nextSource === null) {
                    continue;
                }

                var keysArray = Object.keys(Object(nextSource));
                for (var nextIndex = 0, len = keysArray.length; nextIndex < len; nextIndex++) {
                    var nextKey = keysArray[nextIndex];
                    var desc = Object.getOwnPropertyDescriptor(nextSource, nextKey);
                    if (desc !== undefined && desc.enumerable) {
                        to[nextKey] = nextSource[nextKey];
                    }
                }
            }
            return to;
        }
    });
}

// Promise polyfill
//
(function (global, factory) {
    typeof exports === 'object' && typeof module !== 'undefined' ? factory() :
        typeof define === 'function' && define.amd ? define(factory) :
            (factory());
}(this, (function () { 'use strict';

    /**
     * @this {Promise}
     */
    function finallyConstructor(callback) {
        var constructor = this.constructor;
        return this.then(
            function(value) {
                // @ts-ignore
                return constructor.resolve(callback()).then(function() {
                    return value;
                });
            },
            function(reason) {
                // @ts-ignore
                return constructor.resolve(callback()).then(function() {
                    // @ts-ignore
                    return constructor.reject(reason);
                });
            }
        );
    }

    // Store setTimeout reference so promise-polyfill will be unaffected by
    // other code modifying setTimeout (like sinon.useFakeTimers())
    var setTimeoutFunc = setTimeout;

    function isArray(x) {
        return Boolean(x && typeof x.length !== 'undefined');
    }

    function noop() {}

    // Polyfill for Function.prototype.bind
    function bind(fn, thisArg) {
        return function() {
            fn.apply(thisArg, arguments);
        };
    }

    /**
     * @constructor
     * @param {Function} fn
     */
    function Promise(fn) {
        if (!(this instanceof Promise))
            throw new TypeError('Promises must be constructed via new');
        if (typeof fn !== 'function') throw new TypeError('not a function');
        /** @type {!number} */
        this._state = 0;
        /** @type {!boolean} */
        this._handled = false;
        /** @type {Promise|undefined} */
        this._value = undefined;
        /** @type {!Array<!Function>} */
        this._deferreds = [];

        doResolve(fn, this);
    }

    function handle(self, deferred) {
        while (self._state === 3) {
            self = self._value;
        }
        if (self._state === 0) {
            self._deferreds.push(deferred);
            return;
        }
        self._handled = true;
        Promise._immediateFn(function() {
            var cb = self._state === 1 ? deferred.onFulfilled : deferred.onRejected;
            if (cb === null) {
                (self._state === 1 ? resolve : reject)(deferred.promise, self._value);
                return;
            }
            var ret;
            try {
                ret = cb(self._value);
            } catch (e) {
                reject(deferred.promise, e);
                return;
            }
            resolve(deferred.promise, ret);
        });
    }

    function resolve(self, newValue) {
        try {
            // Promise Resolution Procedure: https://github.com/promises-aplus/promises-spec#the-promise-resolution-procedure
            if (newValue === self)
                throw new TypeError('A promise cannot be resolved with itself.');
            if (
                newValue &&
                (typeof newValue === 'object' || typeof newValue === 'function')
            ) {
                var then = newValue.then;
                if (newValue instanceof Promise) {
                    self._state = 3;
                    self._value = newValue;
                    finale(self);
                    return;
                } else if (typeof then === 'function') {
                    doResolve(bind(then, newValue), self);
                    return;
                }
            }
            self._state = 1;
            self._value = newValue;
            finale(self);
        } catch (e) {
            reject(self, e);
        }
    }

    function reject(self, newValue) {
        self._state = 2;
        self._value = newValue;
        finale(self);
    }

    function finale(self) {
        if (self._state === 2 && self._deferreds.length === 0) {
            Promise._immediateFn(function() {
                if (!self._handled) {
                    Promise._unhandledRejectionFn(self._value);
                }
            });
        }

        for (var i = 0, len = self._deferreds.length; i < len; i++) {
            handle(self, self._deferreds[i]);
        }
        self._deferreds = null;
    }

    /**
     * @constructor
     */
    function Handler(onFulfilled, onRejected, promise) {
        this.onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : null;
        this.onRejected = typeof onRejected === 'function' ? onRejected : null;
        this.promise = promise;
    }

    /**
     * Take a potentially misbehaving resolver function and make sure
     * onFulfilled and onRejected are only called once.
     *
     * Makes no guarantees about asynchrony.
     */
    function doResolve(fn, self) {
        var done = false;
        try {
            fn(
                function(value) {
                    if (done) return;
                    done = true;
                    resolve(self, value);
                },
                function(reason) {
                    if (done) return;
                    done = true;
                    reject(self, reason);
                }
            );
        } catch (ex) {
            if (done) return;
            done = true;
            reject(self, ex);
        }
    }

    Promise.prototype['catch'] = function(onRejected) {
        return this.then(null, onRejected);
    };

    Promise.prototype.then = function(onFulfilled, onRejected) {
        // @ts-ignore
        var prom = new this.constructor(noop);

        handle(this, new Handler(onFulfilled, onRejected, prom));
        return prom;
    };

    Promise.prototype['finally'] = finallyConstructor;

    Promise.all = function(arr) {
        return new Promise(function(resolve, reject) {
            if (!isArray(arr)) {
                return reject(new TypeError('Promise.all accepts an array'));
            }

            var args = Array.prototype.slice.call(arr);
            if (args.length === 0) return resolve([]);
            var remaining = args.length;

            function res(i, val) {
                try {
                    if (val && (typeof val === 'object' || typeof val === 'function')) {
                        var then = val.then;
                        if (typeof then === 'function') {
                            then.call(
                                val,
                                function(val) {
                                    res(i, val);
                                },
                                reject
                            );
                            return;
                        }
                    }
                    args[i] = val;
                    if (--remaining === 0) {
                        resolve(args);
                    }
                } catch (ex) {
                    reject(ex);
                }
            }

            for (var i = 0; i < args.length; i++) {
                res(i, args[i]);
            }
        });
    };

    Promise.resolve = function(value) {
        if (value && typeof value === 'object' && value.constructor === Promise) {
            return value;
        }

        return new Promise(function(resolve) {
            resolve(value);
        });
    };

    Promise.reject = function(value) {
        return new Promise(function(resolve, reject) {
            reject(value);
        });
    };

    Promise.race = function(arr) {
        return new Promise(function(resolve, reject) {
            if (!isArray(arr)) {
                return reject(new TypeError('Promise.race accepts an array'));
            }

            for (var i = 0, len = arr.length; i < len; i++) {
                Promise.resolve(arr[i]).then(resolve, reject);
            }
        });
    };

    // Use polyfill for setImmediate for performance gains
    Promise._immediateFn =
        // @ts-ignore
        (typeof setImmediate === 'function' &&
            function(fn) {
                // @ts-ignore
                setImmediate(fn);
            }) ||
        function(fn) {
            setTimeoutFunc(fn, 0);
        };

    Promise._unhandledRejectionFn = function _unhandledRejectionFn(err) {
        if (typeof console !== 'undefined' && console) {
            console.warn('Possible Unhandled Promise Rejection:', err); // eslint-disable-line no-console
        }
    };

    /** @suppress {undefinedVars} */
    var globalNS = (function() {
        // the only reliable means to get the global object is
        // `Function('return this')()`
        // However, this causes CSP violations in Chrome apps.
        if (typeof self !== 'undefined') {
            return self;
        }
        if (typeof window !== 'undefined') {
            return window;
        }
        if (typeof global !== 'undefined') {
            return global;
        }
        throw new Error('unable to locate global object');
    })();

    if (!('Promise' in globalNS)) {
        globalNS['Promise'] = Promise;
    } else if (!globalNS.Promise.prototype['finally']) {
        globalNS.Promise.prototype['finally'] = finallyConstructor;
    }

})));

// Focus visible
(function (global, factory) {
    typeof exports === 'object' && typeof module !== 'undefined' ? factory() :
        typeof define === 'function' && define.amd ? define(factory) :
            (factory());
}(this, (function () { 'use strict';

    /**
     * Applies the :focus-visible polyfill at the given scope.
     * A scope in this case is either the top-level Document or a Shadow Root.
     *
     * @param {(Document|ShadowRoot)} scope
     * @see https://github.com/WICG/focus-visible
     */
    function applyFocusVisiblePolyfill(scope) {
        var hadKeyboardEvent = true;
        var hadFocusVisibleRecently = false;
        var hadFocusVisibleRecentlyTimeout = null;

        var inputTypesWhitelist = {
            text: true,
            search: true,
            url: true,
            tel: true,
            email: true,
            password: true,
            number: true,
            date: true,
            month: true,
            week: true,
            time: true,
            datetime: true,
            'datetime-local': true
        };

        /**
         * Helper function for legacy browsers and iframes which sometimes focus
         * elements like document, body, and non-interactive SVG.
         * @param {Element} el
         */
        function isValidFocusTarget(el) {
            if (
                el &&
                el !== document &&
                el.nodeName !== 'HTML' &&
                el.nodeName !== 'BODY' &&
                'classList' in el &&
                'contains' in el.classList
            ) {
                return true;
            }
            return false;
        }

        /**
         * Computes whether the given element should automatically trigger the
         * `focus-visible` class being added, i.e. whether it should always match
         * `:focus-visible` when focused.
         * @param {Element} el
         * @return {boolean}
         */
        function focusTriggersKeyboardModality(el) {
            var type = el.type;
            var tagName = el.tagName;

            if (tagName === 'INPUT' && inputTypesWhitelist[type] && !el.readOnly) {
                return true;
            }

            if (tagName === 'TEXTAREA' && !el.readOnly) {
                return true;
            }

            if (el.isContentEditable) {
                return true;
            }

            return false;
        }

        /**
         * Add the `focus-visible` class to the given element if it was not added by
         * the author.
         * @param {Element} el
         */
        function addFocusVisibleClass(el) {
            if (el.classList.contains('focus-visible')) {
                return;
            }
            el.classList.add('focus-visible');
            el.setAttribute('data-focus-visible-added', '');
        }

        /**
         * Remove the `focus-visible` class from the given element if it was not
         * originally added by the author.
         * @param {Element} el
         */
        function removeFocusVisibleClass(el) {
            if (!el.hasAttribute('data-focus-visible-added')) {
                return;
            }
            el.classList.remove('focus-visible');
            el.removeAttribute('data-focus-visible-added');
        }

        /**
         * If the most recent user interaction was via the keyboard;
         * and the key press did not include a meta, alt/option, or control key;
         * then the modality is keyboard. Otherwise, the modality is not keyboard.
         * Apply `focus-visible` to any current active element and keep track
         * of our keyboard modality state with `hadKeyboardEvent`.
         * @param {KeyboardEvent} e
         */
        function onKeyDown(e) {
            if (e.metaKey || e.altKey || e.ctrlKey) {
                return;
            }

            if (isValidFocusTarget(scope.activeElement)) {
                addFocusVisibleClass(scope.activeElement);
            }

            hadKeyboardEvent = true;
        }

        /**
         * If at any point a user clicks with a pointing device, ensure that we change
         * the modality away from keyboard.
         * This avoids the situation where a user presses a key on an already focused
         * element, and then clicks on a different element, focusing it with a
         * pointing device, while we still think we're in keyboard modality.
         * @param {Event} e
         */
        function onPointerDown(e) {
            hadKeyboardEvent = false;
        }

        /**
         * On `focus`, add the `focus-visible` class to the target if:
         * - the target received focus as a result of keyboard navigation, or
         * - the event target is an element that will likely require interaction
         *   via the keyboard (e.g. a text box)
         * @param {Event} e
         */
        function onFocus(e) {
            // Prevent IE from focusing the document or HTML element.
            if (!isValidFocusTarget(e.target)) {
                return;
            }

            if (hadKeyboardEvent || focusTriggersKeyboardModality(e.target)) {
                addFocusVisibleClass(e.target);
            }
        }

        /**
         * On `blur`, remove the `focus-visible` class from the target.
         * @param {Event} e
         */
        function onBlur(e) {
            if (!isValidFocusTarget(e.target)) {
                return;
            }

            if (
                e.target.classList.contains('focus-visible') ||
                e.target.hasAttribute('data-focus-visible-added')
            ) {
                // To detect a tab/window switch, we look for a blur event followed
                // rapidly by a visibility change.
                // If we don't see a visibility change within 100ms, it's probably a
                // regular focus change.
                hadFocusVisibleRecently = true;
                window.clearTimeout(hadFocusVisibleRecentlyTimeout);
                hadFocusVisibleRecentlyTimeout = window.setTimeout(function() {
                    hadFocusVisibleRecently = false;
                }, 100);
                removeFocusVisibleClass(e.target);
            }
        }

        /**
         * If the user changes tabs, keep track of whether or not the previously
         * focused element had .focus-visible.
         * @param {Event} e
         */
        function onVisibilityChange(e) {
            if (document.visibilityState === 'hidden') {
                // If the tab becomes active again, the browser will handle calling focus
                // on the element (Safari actually calls it twice).
                // If this tab change caused a blur on an element with focus-visible,
                // re-apply the class when the user switches back to the tab.
                if (hadFocusVisibleRecently) {
                    hadKeyboardEvent = true;
                }
                addInitialPointerMoveListeners();
            }
        }

        /**
         * Add a group of listeners to detect usage of any pointing devices.
         * These listeners will be added when the polyfill first loads, and anytime
         * the window is blurred, so that they are active when the window regains
         * focus.
         */
        function addInitialPointerMoveListeners() {
            document.addEventListener('mousemove', onInitialPointerMove);
            document.addEventListener('mousedown', onInitialPointerMove);
            document.addEventListener('mouseup', onInitialPointerMove);
            document.addEventListener('pointermove', onInitialPointerMove);
            document.addEventListener('pointerdown', onInitialPointerMove);
            document.addEventListener('pointerup', onInitialPointerMove);
            document.addEventListener('touchmove', onInitialPointerMove);
            document.addEventListener('touchstart', onInitialPointerMove);
            document.addEventListener('touchend', onInitialPointerMove);
        }

        function removeInitialPointerMoveListeners() {
            document.removeEventListener('mousemove', onInitialPointerMove);
            document.removeEventListener('mousedown', onInitialPointerMove);
            document.removeEventListener('mouseup', onInitialPointerMove);
            document.removeEventListener('pointermove', onInitialPointerMove);
            document.removeEventListener('pointerdown', onInitialPointerMove);
            document.removeEventListener('pointerup', onInitialPointerMove);
            document.removeEventListener('touchmove', onInitialPointerMove);
            document.removeEventListener('touchstart', onInitialPointerMove);
            document.removeEventListener('touchend', onInitialPointerMove);
        }

        /**
         * When the polfyill first loads, assume the user is in keyboard modality.
         * If any event is received from a pointing device (e.g. mouse, pointer,
         * touch), turn off keyboard modality.
         * This accounts for situations where focus enters the page from the URL bar.
         * @param {Event} e
         */
        function onInitialPointerMove(e) {
            // Work around a Safari quirk that fires a mousemove on <html> whenever the
            // window blurs, even if you're tabbing out of the page. ¯\_(ツ)_/¯
            if (e.target.nodeName && e.target.nodeName.toLowerCase() === 'html') {
                return;
            }

            hadKeyboardEvent = false;
            removeInitialPointerMoveListeners();
        }

        // For some kinds of state, we are interested in changes at the global scope
        // only. For example, global pointer input, global key presses and global
        // visibility change should affect the state at every scope:
        document.addEventListener('keydown', onKeyDown, true);
        document.addEventListener('mousedown', onPointerDown, true);
        document.addEventListener('pointerdown', onPointerDown, true);
        document.addEventListener('touchstart', onPointerDown, true);
        document.addEventListener('visibilitychange', onVisibilityChange, true);

        addInitialPointerMoveListeners();

        // For focus and blur, we specifically care about state changes in the local
        // scope. This is because focus / blur events that originate from within a
        // shadow root are not re-dispatched from the host element if it was already
        // the active element in its own scope:
        scope.addEventListener('focus', onFocus, true);
        scope.addEventListener('blur', onBlur, true);

        // We detect that a node is a ShadowRoot by ensuring that it is a
        // DocumentFragment and also has a host property. This check covers native
        // implementation and polyfill implementation transparently. If we only cared
        // about the native implementation, we could just check if the scope was
        // an instance of a ShadowRoot.
        if (scope.nodeType === Node.DOCUMENT_FRAGMENT_NODE && scope.host) {
            // Since a ShadowRoot is a special kind of DocumentFragment, it does not
            // have a root element to add a class to. So, we add this attribute to the
            // host element instead:
            scope.host.setAttribute('data-js-focus-visible', '');
        } else if (scope.nodeType === Node.DOCUMENT_NODE) {
            document.documentElement.classList.add('js-focus-visible');
            document.documentElement.setAttribute('data-js-focus-visible', '');
        }
    }

    // It is important to wrap all references to global window and document in
    // these checks to support server-side rendering use cases
    // @see https://github.com/WICG/focus-visible/issues/199
    if (typeof window !== 'undefined' && typeof document !== 'undefined') {
        // Make the polyfill helper globally available. This can be used as a signal
        // to interested libraries that wish to coordinate with the polyfill for e.g.,
        // applying the polyfill to a shadow root:
        window.applyFocusVisiblePolyfill = applyFocusVisiblePolyfill;

        // Notify interested libraries of the polyfill's presence, in case the
        // polyfill was loaded lazily:
        var event;

        try {
            event = new CustomEvent('focus-visible-polyfill-ready');
        } catch (error) {
            // IE11 does not support using CustomEvent as a constructor directly:
            event = document.createEvent('CustomEvent');
            event.initCustomEvent('focus-visible-polyfill-ready', false, false, {});
        }

        window.dispatchEvent(event);
    }

    if (typeof document !== 'undefined') {
        // Apply the polyfill to the global document, so that no JavaScript
        // coordination is required to use the polyfill in the top-level document:
        applyFocusVisiblePolyfill(document);
    }

})));

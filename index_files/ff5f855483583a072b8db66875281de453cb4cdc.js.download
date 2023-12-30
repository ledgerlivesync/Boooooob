window.$$ = {
  jqueryPlugin: jqueryPlugin,
  initPlugins: initPlugins,
  getDuration: getDuration,
  Request: Request,
  template: template,
  createUID: createUID,
  url: {
    queryToJson: function(query) {
      query = query || window.location.search;
      var json = {};

      query
        .replace('?', '')
        .split('&')
        .map(function(item) {
          return item.split('=')
        })
        .forEach(function(item) {
          if (item[0]) json[item[0]] = item[1] ? decodeURIComponent(item[1]) : true;
        });

      return json;
    },

    jsonToQuery: function(json) {
      json = json || {};
      var query = '?';

      Object.keys(json).forEach(function(key, i) {
        if (i) query += '&';
        query += key + '='+ encodeURIComponent(json[key]);
      });

      return query;
    }

  },
  page: {
    /**
     * Is home page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isHomePage: function isHomePage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /^http(s)?:\/\/[^\/?#]+(\/hc(\/[a-z-_]+)?(\/)?([?]([^?\/]+)?)?([#]([^#\/]+)?)?)?$/.test(url);
    },

    /**
     * Is category page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isCategoryPage: function isCategoryPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?categories\//i.test(url);
    },

    /**
     * Is section page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isSectionPage: function isSectionPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?sections\//i.test(url);
    },

    /**
     * Is article page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isArticlePage: function isArticlePage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?articles\//i.test(url);
    },

    /**
     * Is search results page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isSearchResultsPage: function isSearchResultsPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?search\?*.*/i.test(url);
    },

    /**
     * Is contributions posts page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isContributionsPostsPage: function isContributionsPostsPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?contributions\/posts(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is contributions community comments page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isContributionsCommunityCommentsPage: function isContributionsCommunityCommentsPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?contributions\/community_comments(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is contributions community comments page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isContributionsCommentsPage: function isContributionsCommentsPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?contributions\/comments(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is following page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isFollowingPage: function isFollowingPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?subscriptions(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is request list page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isRequestListPage: function isRequestListPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?requests(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is request page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isRequestPage: function isRequestPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return !this.isNewRequestPage(url) && /\/hc\/([a-z-]+\/)?requests\/[^/?#]+(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is new request page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isNewRequestPage: function isNewRequestPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?requests\/new(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is community topic list page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isCommunityTopicListPage: function isCommunityTopicListPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?community\/topics(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is community topic page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isCommunityTopicPage: function isCommunityTopicPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?community\/topics\/[^\/?#]+(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is community post list page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isCommunityPostListPage: function isCommunityPostListPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?community\/posts(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is community post page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isCommunityPostPage: function isCommunityPostPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return !this.isNewCommunityPostPage(url) && /\/hc\/([a-z-]+\/)?community\/posts\/[^\/?#]+(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is new community post page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isNewCommunityPostPage: function isNewCommunityPostPage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?community\/posts\/new(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Is user profile page
     * @param {string?} pageUrl: page url
     * @return {boolean}
     */
    isUserProfilePage: function isUserProfilePage(pageUrl) {
      var url = pageUrl || window.location.href;
      return /\/hc\/([a-z-]+\/)?profiles\/[^\/?#]+(\/)?([?#].*)?$/i.test(url);
    },

    /**
     * Get page ID
     * @param {string?} pageUrl: page url
     * @return {number | null}: page ID or null
     */
    getPageId: function getPageId(pageUrl) {
      var url = pageUrl || window.location.href;
      var links = url.split('/');
      var result = links[links.length - 1];
      return parseInt(result, 10) || null;
    },

    /**
     * Get category ID
     * @param {string?} url: page url
     * @return {number | null}: category ID or null
     */
    getCategoryId: function getCategoryId(url) {
      if (this.isCategoryPage(url)) {
        return this.getPageId(url);
      } else if (!url) {
        var $breadcrumbsItems = $('.breadcrumbs li');

        for (var i = 0; i < $breadcrumbsItems.length; i++) {
          var linkUrl = $breadcrumbsItems.eq(i).find('a').attr('href');

          if (linkUrl && this.isCategoryPage(linkUrl)) {
            return this.getPageId(linkUrl);
          }
        }
      }

      return null;
    },

    /**
     * Get section ID
     * @param {string?} url: page url
     * @return {number | null}: section ID or null
     */
    getSectionId: function getSectionId(url) {
      if (this.isSectionPage(url)) {
        return this.getPageId(url);
      } else if (!url) {
        var $breadcrumbsItems = $('.breadcrumbs li');

        for (var i = $breadcrumbsItems.length - 1; i >= 0; i--) {
          var linkUrl = $breadcrumbsItems.eq(i).find('a').attr('href');

          if (linkUrl && this.isSectionPage(linkUrl)) {
            return this.getPageId(linkUrl);
          }
        }
      }

      return null;
    },

    /**
     * Get article ID
     * @param {string?} url: page url
     * @return {number | null}: article ID or null
     */
    getArticleId: function getArticleId(url) {
      if (this.isArticlePage(url)) {
        return this.getPageId(url);
      }

      return null;
    }
  }
};

/**
 * Create jquery plugin
 */
Object.defineProperty(window.$$, 'registeredPlugins', {
  value: [],
  enumerable: false
});

Object.defineProperty(window.$$, 'plugins', {
  value: {},
  enumerable: false
});

function Plugin() {}

function jqueryPlugin(name, opts) {
  var api            = opts.api,
      defaultOptions = opts.defaultOptions,
      methods        = opts.methods;

  window.$$.registeredPlugins.push(name);
  window.$$.plugins[name] = [];

  $.fn[name] = function() {
    var $container = this;

    // Call api method
    if (arguments[0] && typeof arguments[0] === 'string') {
      var apiName = arguments[0],
          params  = Array.prototype.splice.call(arguments, 0, 1);

      if (!api || api.indexOf(apiName) === -1) {
        return console.error('The "' + name + '" plugin doesn\'t have "' + apiName + '" API method');
      }

      var plugin = window.$$.plugins[name]
        .filter(function(plugin) {
          return plugin.$container[0] === $container[0];
        })[0];

      if (plugin) {
        return plugin[apiName].apply(plugin, params);
      }
    }
    // Init plugin
    else {
      var uid = 'f' + (~~(Math.random() * 1e8)).toString(16);
      var pluginOptions = $.extend({}, defaultOptions, arguments && arguments[0] ? arguments[0] : {});

      Object.defineProperty(pluginOptions, '_default', {
        value: $.extend({}, defaultOptions || {}),
        enumerable: false
      });

      Object.defineProperty(pluginOptions, '_props', {
        value: arguments && arguments[0] ? arguments[0] : {},
        enumerable: false
      });

      var plugins = [];

      $container.each(function(i, element) {
        var plugin = new Plugin();

        try {
          createMethods(plugin, methods);
          plugin.init(uid, pluginOptions, $(element));
        }
        catch (error) {
          createError(element, name, error);
        }

        window.$$.plugins[name].push(plugin);
        plugins.push(plugin);
      });

      return plugins;
    }
  };

  function createMethods(plugin, methods) {
    Object.keys(methods).forEach(function(name) {
      if (methods.hasOwnProperty(name)) {
        var descriptor = {
          value: methods[name],
          enumerable: true,
          configurable: false,
          writable: true
        };

        Object.defineProperty(plugin, name, descriptor);
      }
    });
  }

  function createError(element, name, error) {
    console.group(
      '%cjQuery plugin error %c' + getElementString(element) + '[data-plugin-' + name + ']',
      'color: #da4949;',
      'font-weight: normal;'
    );
    console.error('Element:', $(element));
    console.error('Path:', getElementPath(element));
    console.error(error);
    console.groupEnd();
  }

  function getElementPath(element, root) {
    var str = getElementString(element) + (root ? ('\n    > ' + root) : '');

    return element.localName !== 'body'
      ? getElementPath(element.parentNode, str)
      : str;
  }

  function getElementString(element) {
    return element.localName +
      element.id.replace(/^(\S)/i, '#$1') +
      element.className.replace(/\s+/g, '.').replace(/^(\S)/i, '.$1');
  }
}

/**
 * Plugins initialization
 */
function initPlugins($container) {
  $container = $container || $(document);
  window.$$.registeredPlugins.forEach(function(pluginName) {
    var name = pluginName.replace(/([A-Z])/g, function(match, symbol) { return '-' + symbol.toLowerCase();});
    $container.find('[data-plugin-' + name + ']').each(function(i, element) {
      var $element = $(element);
      var options = $element.data('plugin-' + name) || {};
      $element[pluginName](options);
    });
  });
}

var initialized = false;
var pageIsLoaded = false;
var scriptIsLoaded = false;

$(window).on('page:loaded', function() {
  pageIsLoaded = true;
  if (scriptIsLoaded) {
    initialized = true;
    $$.initPlugins();
  }
});

$(window).on('script:loaded', function() {
  scriptIsLoaded = true;
  if (pageIsLoaded) {
    initialized = true;
    $$.initPlugins();
  }
});

$(function() {
  if (!initialized) {
    $$.initPlugins();
  }
});

/**
 * Fetch and return a transition duration of the element
 * @param {jQuery} $element
 * @return {number} - duration
 */
function getDuration($element) {
  var defaultDuration = 400;
  var transitionDuration = getComputedStyle($element[0])['transitionDuration'];

  var duration;

  if (transitionDuration.indexOf('ms') !== -1) {
    duration = Number(transitionDuration.replace('ms', ''));
  }
  else if (transitionDuration.indexOf('s') !== -1) {
    duration = Number(transitionDuration.replace('s', '')) * 1000;
  }

  return duration || defaultDuration;
}

/**
 * Request
 */

var requestIndex = 0;
var requestPending = {};
var requestDefaultOptions = {

  // SessionStorage prefix
  ssPrefix: 'request',

  // Cache lifetime
  cacheLifetime: 1000 * 60, // 1 min

  // Request type method
  type: 'GET'
};

function Request(options) {
  this.options = Object.assign({}, requestDefaultOptions, options);
  this.ssKeys = [];
  this.requestIndex = requestIndex++;
}

/**
 * Fetch data
 * @param {string} url - api url
 * @param {object?} data - request data
 * @param {boolean?} replace - replace storage data
 * @return {Promise<object>}
 */
Request.prototype.fetch = function(url, data, replace) {
  data = data || {};

  var ssKey = this.options.ssPrefix + ':' + window.locale + ':' + url + ':' + JSON.stringify(data);
  var ssData = sessionStorage.getItem(ssKey);
  var jsonData = ssData ? JSON.parse(ssData) : {};

  if (!ssData || !jsonData.data || replace) {
    return new Promise(function(resolve, reject) {
      if (!requestPending[ssKey]) {
        requestPending[ssKey] = {
          index: this.requestIndex,
          successHandlers: [],
          errorHandlers: []
        };

        $.ajax({
          type: this.options.type,
          url: url,
          data: data,
          success: function(data) {
            this.fetchSuccess(resolve, data, ssKey);
          }.bind(this),
          error: function(error) {
            this.fetchError(reject, error, ssKey);
          }.bind(this)
        });
      }
      else {
        requestPending[ssKey].successHandlers.push([this.fetchSuccess.bind(this), resolve]);
        requestPending[ssKey].errorHandlers.push([this.fetchError.bind(this), reject]);
      }
    }.bind(this));
  }
  else {
    if (this.ssKeys.indexOf(ssKey) === -1) {
      this.ssKeys.push(ssKey);
    }

    if (jsonData.date + this.options.cacheLifetime < Date.now()) {
      this.fetch(url, data, true).then();
    }

    return new Promise(function(resolve) {
      resolve(jsonData.data);
    });
  }
};

/**
 * Success response handler
 * @protected
 * @param {function} resolve - Promise resolve function
 * @param {string|Object} data - Response data
 * @param {string} ssKey - sessionStorage item kay
 */
Request.prototype.fetchSuccess = function(resolve, data, ssKey) {
  this.ssKeys.push(ssKey);

  sessionStorage.setItem(ssKey, JSON.stringify({
    date: Date.now(),
    data: data
  }));

  resolve(data);

  this.deleteRequestPending('successHandlers', data, ssKey);
};

/**
 * Error response handler
 * @protected
 * @param {function} reject - Promise reject function
 * @param {Error|String} error
 * @param {string} ssKey - sessionStorage item kay
 */
Request.prototype.fetchError = function(reject, error, ssKey) {
  reject(error);

  this.deleteRequestPending('errorHandlers', error, ssKey);
};

Request.prototype.deleteRequestPending = function(handlerName, data, ssKey) {
  if (requestPending[ssKey].index !== this.requestIndex) {
    return;
  }

  if (requestPending[ssKey][handlerName].length) {
    requestPending[ssKey][handlerName].forEach(function(handler) {
      handler[0](handler[1], data, ssKey);
    });
  }

  delete requestPending[ssKey];
};

// Simple JavaScript Templating
// John Resig - https://johnresig.com/ - MIT Licensed
var cache = {};

function template(str, data) {
  // Figure out if we're getting a template, or if we need to
  // load the template - and be sure to cache the result.
  var fn = !/\W/.test(str)
    ? cache[str] = cache[str] || template(document.getElementById(str).innerHTML)
    :

    // Generate a reusable function that will serve as a template
    // generator (and which will be cached).
    new Function('obj',
      'var p=[],print=function(){p.push.apply(p,arguments);};' +

      // Introduce the data as local variables using with(){}
      'with(obj){p.push(\'' +

      // Convert the template into pure JavaScript
      str
        .replace(/[\r\t\n]/g, ' ')
        .split('<%').join('\t')
        .replace(/((^|%>)[^\t]*)'/g, '$1\r')
        .replace(/\t=(.*?)%>/g, '\',$1,\'')
        .split('\t').join('\');')
        .split('%>').join('p.push(\'')
        .split('\r').join('\\\'')
      + '\');}return p.join(\'\');');

  // Provide some basic currying to the user
  return data ? fn(data) : fn;
}

/**
 * Creates and returns an unique ID
 * @return {string}
 */
function createUID() {
  return 'f' + (~~(Math.random() * 1e8)).toString(16);
}

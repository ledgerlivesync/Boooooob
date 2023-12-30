/**
 * This script uses the utils object "$$"
 *
 * $$.jqueryPlugin(pluginName, options);
 *      Creates a jQuery plugin and call this after the "page:loaded" event
 *
 *      options:
 *          api?: [] => jquery plugin api to the call as $(element).plugin('apiMethod', ...options);
 *          defaultOptions?: {} => jquery plugin default options
 *          methods: {} => jquery plugin methods. The "init" method is required
 *
 * $$.initPlugins($container?);
 *      Initializes all plugins in the container
 *
 * $$.Request(options?).fetch(url, data?);
 *      Fetches a data from the sessionStorage or makes request to the server
 *      Returns the <Promise>
 *
 *      options:
 *          ssPrefix: 'request' => SessionStorage prefix
 *          type: 'GET' => type of the request
 *
 * $$.getDuration($element);
 *      Fetches and returns a transition duration of the element
 */

/**
 * jQuery special event for detecting single enter key press
 *
 * @example:
 *
 * $('.btn').on('tap enterkey', ...);
 */

var notDefaultLanguage = window.location.href.indexOf('/en-us/') == -1;
var isArticle = window.location.href.indexOf('/articles/') > -1;
var isErrorPage = $(".error-page").length > 0;


if ( isArticle && notDefaultLanguage && isErrorPage ) {
    var newURL = window.location.href.replace(/(.*\/hc\/)([\w-]+)(\/.*)/, "$1en-us$3");
    window.location.href =  newURL;
}  




$.event.special.enterkey = {
  delegateType: 'keydown',
  bindType: 'keydown',
  handle: function(event) {
    var handleObj = event.handleObj;
    var ret = null;

    if (event.which === 13) {
      event.type = handleObj.origType;
      ret = handleObj.handler.apply(this, arguments);
      event.type = handleObj.type;
      return ret;
    }
  }
};

/**
 * jQuery special event for detecting single escape key press
 *
 * @example:
 *
 * $('.btn').on('tap escapekey', ...);
 */
$.event.special.escapekey = {
  delegateType: 'keydown',
  bindType: 'keydown',
  handle: function(event) {
    var handleObj = event.handleObj;
    var ret = null;

    if (event.which === 27) {
      event.type = handleObj.origType;
      ret = handleObj.handler.apply(this, arguments);
      event.type = handleObj.type;
      return ret;
    }
  }
};

/**
 * Dropdown plugin
 * This plugin use the "nanopop" package for setting dropdown positions.
 * More information https://github.com/Simonwep/nanopop
 *
 * @example:
 *
 * <div class="dropdown" data-plugin-dropdown>
 *     <a class="js-dropdown-open" tabindex="0" role="button" aria-label="Open">
 *         Open dropdown
 *     </a>
 *     <div class="dropdown__content js-dropdown" role="menu" aria-hidden="true">
 *         <a href="#" class="dropdown__menuitem" rel="nofollow" role="menuitem">One</a>
 *         <a href="#" class="dropdown__menuitem" rel="nofollow" role="menuitem">Two</a>
 *         <a href="#" class="dropdown__menuitem" rel="nofollow" role="menuitem">Three</a>
 *     </div>
 * </div>
 */
$$.jqueryPlugin('dropdown', {
  api: ['open', 'close'],
  defaultOptions: {
    // Open link selector.
    openLinkSelector: '.js-dropdown-open',

    // Close button selector. By default - all links with a "href" attribute in the dropdown container
    closeLinkSelector: '.js-dropdown a[href]',

    // Dropdown container selector
    dropdownSelector: '.js-dropdown',

    // Preferred position, any combination of [top|right|bottom|left]-[start|middle|end] is valid.
    // 'middle' is used as default-variant if you leave it out.
    position: 'bottom',

    // Margin between the dropdown and the trigger button
    margin: 12,

    // Add dropdown chevron
    addChevron: true,

    // jQuery event name which is triggered when dropdown is about to be opened
    eventOpen: 'dropdown:open',

    // jQuery event name which is triggered when dropdown is about to be closed
    eventClose: 'dropdown:close'
  },
  methods: {
    init: function(uid, options, $container) {
      this.uid = uid;
      this.options = options;
      this.lastPosition = '';
      this.popper = null;
      this.isOpened = false;

      this.$container = $container;
      this.$dropdown = this.$container.find(this.options.dropdownSelector);
      this.$trigger = this.$container.find(this.options.openLinkSelector).first();
      this.$document = $(document);
      this.$window = $(window);
      this.chevron = $();

      if (this.options.addChevron) {
        this.chevron = $('<div class="dropdown__chevron"></div>');
        this.$dropdown.append(this.chevron[0]);
      }

      this.bindEvents();
    },

    bindEvents: function() {
      var click = 'click.' + this.uid + ' enterkey.' + this.uid;

      this.$container.on(click, this.options.openLinkSelector, this.handleDropdownToggle.bind(this));
      this.$container.on(click, this.options.closeLinkSelector, this.close.bind(this));
      this.$document.on(click, this.handleDropdownClose.bind(this));
      this.$document.on('escapekey.' + this.uid, this.close.bind(this));
    },

    handleDropdownToggle: function(event) {
      event.preventDefault();
      if (!this.isOpened) {
        this.open($(event.currentTarget));
      }
      else {
        this.close();
      }
    },

    handleDropdownClose: function(event) {
      if (this.isOpened &&
        event.target !== this.$container[0] &&
        !$(event.target).closest(this.$container[0]).length
      ) {
        this.close();
      }
    },

    open: function($trigger) {
      if (!this.isOpened) {
        this.isOpened = true;

        if ($trigger) {
          this.$trigger = $trigger;
          this.$trigger.addClass('is-active');
        }

        this.update();
        this.$window.on('resize.' + this.uid, this.update.bind(this));
        this.$container.trigger(this.options.eventOpen, this);
      }
    },

    close: function() {
      if (this.isOpened) {
        this.isOpened = false;
        this.$dropdown.removeClass('is-active');
        this.$trigger.removeClass('is-active');
        this.$window.off('resize.' + this.uid);
        this.$container.trigger(this.options.eventClose, this);
      }
    },

    update: function() {
      this.popper = NanoPop.createPopper(this.$trigger[0], this.$dropdown[0], Object.assign({}, this.options, {
        container: document.documentElement.getBoundingClientRect()
      }));
      this.$dropdown.removeClass('dropdown__content--' + this.lastPosition);
      this.lastPosition = this.popper.update();
      this.$dropdown.addClass('is-active dropdown__content--' + this.lastPosition);

      if (this.options.addChevron) {
        this.setChevronPosition();
      }
    },

    setChevronPosition: function() {
      this.chevron
        .removeAttr('class style')
        .addClass('dropdown__chevron dropdown__chevron--' + this.lastPosition);

      var triggerRect = this.$trigger[0].getBoundingClientRect();
      var chevronRect = this.chevron[0].getBoundingClientRect();

      if (/^[tb]/i.test(this.lastPosition)) {
        this.chevron.offset({left: triggerRect.x + triggerRect.width / 2 - chevronRect.width / 2});
      }
      else {
        this.chevron.offset({top: triggerRect.y + triggerRect.height / 2 - chevronRect.height / 2});
      }
    }
  }
});

/**
 * Tabs plugin
 *
 * @example:
 *
 * <div class="tabs tabs--type-1 tabs--style-1" data-plugin-tabs>
 *     <div class="tabs__links js-tabs-links">
 *         <a href="#one" tabindex="0">One</a>
 *         <a href="#two" tabindex="0">Two</a>
 *         <a href="#three" tabindex="0">Three</a>
 *     </div>
 *     <div class="tabs__containers js-tabs-containers">
 *         <div id="one" class="tabs__container" aria-hidden="true">Content 1</div>
 *         <div id="two" class="tabs__container" aria-hidden="true">Content 2</div>
 *         <div id="three" class="tabs__container" aria-hidden="true">Content 3</div>
 *     </div>
 * </div>
 */
$$.jqueryPlugin('tabs', {
  api: ['toggle', 'destroy'],
  defaultOptions: {
    // Index of the Default active tab
    activeIndex: 0,

    // Selector of the container with links
    linksSelector: '.js-tabs-links',

    // Selector of the container with tabs
    containersSelector: '.js-tabs-containers',

    // jQuery event name which is triggered when tab is about to be opened
    eventOpen: 'tabs:open',

    // jQuery event name which is triggered after tab is opened
    eventOpened: 'tabs:opened'
  },
  methods: {
    init: function(uid, options, $container) {
      this.uid = uid;
      this.options = options;
      this.tabsContainerDelay = 10;
      this.triggerDuration = 400;
      this.tabDuration = 400;
      this.duration = 400;

      this.$container = $container;
      this.$links = this.$container.find(this.options.linksSelector).first();
      this.$containers = this.$container.find(this.options.containersSelector).first();
      this.$trigger = $();
      this.$tab = $();
      this.$lastTrigger = $();
      this.$lastTab = $();

      if (this.options.activeIndex !== -1) {
        this.toggle(0);
      }

      this.bindEvents();
    },

    bindEvents: function() {
      var click = 'click.' + this.uid + ' enterkey.' + this.uid;

      this.$links.on(click, 'a', this.handleTabsToggle.bind(this));
    },

    handleTabsToggle: function(event) {
      event.preventDefault();
      this.toggle($(event.currentTarget));
    },

    toggle: function($triggerOrIndex) {
      var $links = this.$links.children('a');
      var $containers = this.$containers.children('div');

      this.$lastTrigger = $links.filter('.is-active');
      this.$lastTab = $containers.filter('.is-active');

      this.$trigger = typeof $triggerOrIndex === 'number'
        ? $links.eq($triggerOrIndex)
        : $triggerOrIndex;

      if (!this.$trigger.length) {
        return;
      }

      var activeTabId = this.$trigger.attr('href').replace('#', '');
      this.$tab = $containers.filter('[id="' + activeTabId + '"]');

      this.$container.trigger(this.options.eventOpen, this);

      this.triggerDuration = $$.getDuration(this.$trigger);
      this.tabDuration = $$.getDuration(this.$tab);
      this.duration = this.triggerDuration > this.tabDuration ? this.triggerDuration : this.tabDuration;

      this.$containers.css('height', this.$containers.height());

      setTimeout(function() {
        this.$containers.css('height', this.$tab.outerHeight());
      }.bind(this), this.tabsContainerDelay);

      setTimeout(function() {
        this.$containers.css('height', '');
        this.$container.trigger(this.options.eventOpened, this);
      }.bind(this), this.tabsContainerDelay + this.duration);

      this.$lastTrigger.add(this.$lastTab).removeClass('is-active');
      this.$lastTab.attr('aria-hidden', 'true');
      this.$trigger.add(this.$tab).addClass('is-active');
      this.$tab.attr('aria-hidden', 'false');
    },

    destroy: function() {
      this.$links.off('.' + this.uid);
    }
  }
});

/**
 * Spoiler plugin
 *
 * @example
 * <div class="spoiler spoiler--type-1 spoiler--style-2" data-plugin-spoiler>
 *     <a class="spoiler__title js-spoiler-toggle" role="button" tabindex="0">Spoiler title</a>
 *     <div class="spoiler__text js-spoiler" aria-hidden="true">
 *         Spoiler content
 *     </div>
 * </div>
 */
$$.jqueryPlugin('spoiler', {
  api: ['toggle', 'open', 'close', 'destroy'],
  defaultOptions: {
    // Spoiler is open
    open: false,

    // Selector of the toggle link
    linkSelector: '.js-spoiler-toggle',

    // Selector of the spoiler box
    spoilerSelector: '.js-spoiler',

    // Animation duration
    duration: 400,

    // jQuery event name which is triggered when spoiler is about to be opened
    eventOpen: 'spoiler:open',

    // jQuery event name which is triggered after spoiler is opened
    eventOpened: 'spoiler:opened',

    // jQuery event name which is triggered when spoiler is about to be closed
    eventClose: 'spoiler:close',

    // jQuery event name which is triggered after spoiler is closed
    eventClosed: 'spoiler:closed'
  },
  methods: {
    init: function(uid, options, $container) {
      this.uid = uid;
      this.options = options;

      this.$container = $container;
      this.$link = this.$container.find(this.options.linkSelector).first();
      this.$spoiler = this.$container.find(this.options.spoilerSelector).first();

      if (this.options.open) {
        this.isOpen = false;
        this.open(true);
      }
      else {
        this.isOpen = true;
        this.close(true);
      }

      this.bindEvents();
    },

    bindEvents: function() {
      var click = 'click.' + this.uid + ' enterkey.' + this.uid;

      this.$link.on(click, this.handleSpoilerToggle.bind(this));
    },

    handleSpoilerToggle: function(event) {
      event.preventDefault();
      this.toggle();
    },

    toggle: function() {
      if (this.isOpen) {
        this.close();
      }
      else {
        this.open();
      }
    },

    open: function(withoutAnimation) {
      if (!this.isOpen) {
        this.isOpen = true;
        this.$container.addClass('is-open');
        this.$link.add(this.$spoiler).addClass('is-active');
        this.$container.trigger(this.options.eventOpen, this);

        var duration = withoutAnimation ? 0 : this.options.duration;
        this.$spoiler.attr('aria-hidden', 'false').slideDown(duration, function() {
          this.$container.trigger(this.options.eventOpened, this);
        }.bind(this));
      }
    },

    close: function(withoutAnimation) {
      if (this.isOpen) {
        this.isOpen = false;
        this.$container.removeClass('is-open');
        this.$link.add(this.$spoiler).removeClass('is-active');
        this.$container.trigger(this.options.eventClose, this);

        var duration = withoutAnimation === true ? 0 : this.options.duration;
        this.$spoiler.attr('aria-hidden', 'true').slideUp(duration, function() {
          this.$container.trigger(this.options.eventClosed, this);
        }.bind(this));
      }
    },

    destroy: function() {
      this.$link.off('.' + this.uid);
    }
  }
});

/**
 * Accordion plugin
 * Groups spoilers into an accordion
 *
 * @example:
 * <div class="accordion accordion--type-1 accordion--style-2" data-plugin-accordion>
 *     <div class="spoiler" data-plugin-spoiler>...</div>
 *     <div class="spoiler" data-plugin-spoiler>...</div>
 *     <div class="spoiler" data-plugin-spoiler>...</div>
 * </div>
 */
$$.jqueryPlugin('accordion', {
  api: ['open', 'destroy'],
  defaultOptions: {
    // Index of the Default active spoiler
    activeIndex: -1,

    // Only one active spoiler in the accordion
    oneActive: true
  },
  methods: {
    init: function(uid, options, $container) {
      this.uid = uid;
      this.options = options;

      this.$container = $container;
      this.$spoilers = this.$container.children('[data-plugin-spoiler]');

      this.open(this.options.activeIndex, true);
      this.bindEvents();
    },

    bindEvents: function() {
      if (this.options.oneActive) {
        this.$spoilers.on('spoiler:open.' + this.uid, this.handleSpoilerOpen.bind(this));
      }
    },

    handleSpoilerOpen: function(event, spoiler) {
      this.$spoilers.each(function(i, element) {
        if (element !== spoiler.$container[0]) {
          $(element).spoiler('close');
        }
      });
    },

    open: function(index, withoutAnimation) {
      this.$spoilers.each(function(i, element) {
        var method = i === index ? 'open' : 'close';
        $(element).spoiler(method, withoutAnimation);
      });
    },

    destroy: function() {
      this.$spoilers.off('.' + this.uid);
    }
  }
});

/**
 * Styles elements from the article body
 */
$$.jqueryPlugin('articleBody', {
  methods: {
    init: function(uid, options, $container) {
      this.$container = $container;
      this.createIframe();
      this.createTable();
    },

    createIframe: function() {
      var $iframeContainer = $('<div class="iframe"></div>');
      var $iframe = this.$container.find('iframe');
      var height = $iframe.height() / $iframe.width() * 100;
      $iframe.after($iframeContainer);
      $iframeContainer.append($iframe).css('padding-bottom', height + '%');
    },

    createTable: function() {
      this.$container.find('table').each(function(i, element) {
        var $table = $(element);
        var $tableContainer = $('<div class="table-container"></div>');
        $table.after($tableContainer);
        $tableContainer.append($table);
      });
    }
  }
});

/**
 * Auto submit filtering form
 */
$$.jqueryPlugin('autosubmit', {
  methods: {
    init: function(uid, options, $container) {
      $container.find('input, select').on('change', function(event) {
        $(event.currentTarget).closest('form').submit();
      });
    }
  }
});

$$.jqueryPlugin('brand', {
  defaultOptions: {
    lsName: 'theme:brand',
    defaultBrand: 'support' // docs | support
  },

  methods: {
    init: function(uid, options, $container) {
      this.brand = '';
      this.options = options;
      this.query = $$.url.queryToJson();

      this.$container = $container;
      this.$supportLink = $('.js-support-link');
      this.$docsLink = $('.js-docs-link');
      this.$body = $('body');

      this.fetchCurrentBrand();

      if (window.isHomePage) {
        this.redirectToBrand();
      }
      else if (!window.isLoginPage) {
        this.setBrand();
      }
    },

    fetchCurrentBrand: function() {
      var categoryId = $$.page.getCategoryId();

      if (window.brendForce) {
        this.brand = window.brendForce;
      }
      else if (categoryId && (categoryId === window.theme.settings.docsId || categoryId === window.theme.settings.supportId)) {
        this.brand = categoryId === window.theme.settings.docsId ? 'docs' : 'support';
      }
      else if (this.query.docs) {
        this.brand = 'docs';
      }
      else if (this.query.support) {
        this.brand = 'support';
      }
      else {
        var lsBrand = localStorage.getItem(this.options.lsName);
        if (lsBrand && lsBrand === 'docs' || 'support') {
          this.brand = lsBrand;
        }
        else {
          this.brand = this.options.defaultBrand;
        }
      }
    },

    setBrand: function() {
      this.$body.removeClass('brand-docs brand-support').addClass('brand-' + this.brand);

      this.$supportLink
        .toggleClass('is-active', this.brand === 'support')
        .attr('href', '/hc/categories/' + window.theme.settings.supportId + '?support=true');

      this.$docsLink
        .toggleClass('is-active', this.brand === 'docs')
        .attr('href', '/hc/categories/' + window.theme.settings.docsId + '?docs=true');

      $('.logo').attr('href', '/hc' + (this.brand === 'support' ? '?support=true' : '?docs=true'));

      if (this.brand === 'support') {
        $('.visible-docs').remove();
      }
      else if (this.brand === 'docs') {
        $('.visible-support').remove();
      }

      localStorage.setItem(this.options.lsName, this.brand);

      if (!this.query[this.brand]) {
        delete this.query.support;
        delete this.query.docs;
        this.query[this.brand] = true;

        var query = $$.url.jsonToQuery(this.query);
        history.replaceState(null, null, window.location.origin + window.location.pathname + query);
      }
    },

    redirectToBrand: function() {
      var url = '/hc/categories/' + (this.brand === 'docs' ? window.theme.settings.docsId : window.theme.settings.supportId) + '?' + this.brand;

      localStorage.setItem(this.options.lsName, this.brand);
      window.location.replace(window.location.origin + url);
    }
  }
});

$$.jqueryPlugin('status', {
  defaultOptions: {
    domain: ''
  },
  methods: {
    init: function(uid, options, $container) {
      if (!options.domain) return;

      var endpoint = options.domain.replace(/\/$/, '') + '/api/v2/status.json';

      $.get(endpoint, function(data) {
        var description = data.status.description;

        if (window.theme.translations && window.theme.translations[data.status.description]) {
          description = window.theme.translations[data.status.description];
        }

        $container.addClass('status status--' + data.status.indicator);
        $container.html('<a href="' + options.domain + '">' + description + '</a>');
        $container.removeClass('is-hidden');
      });
    }
  }
});

$$.jqueryPlugin('sectionIcon', {
  defaultOptions: {
    sectionId: 0
  },
  methods: {
    init: function(uid, options, $container) {
      this.$container = $container;

      if (options.sectionId && window.theme.settings.icons[options.sectionId]) {
        this.setIcon(options.sectionId);
      }
      else {
        var url = $('.breadcrumbs li').eq(2).find('a').attr('href');
        var sectionId = $$.page.getSectionId(url);

        if (url && sectionId) {
          this.setIcon(sectionId);
        }
      }
    },

    setIcon: function(sectionId) {
      if (/\.svg$/.test(window.theme.settings.icons[sectionId])) {
        $.get(window.theme.settings.icons[sectionId], function(svgDocument) {
          var $svg = $(svgDocument).find('svg');
          $svg.find('[fill="#f3f3f3"], [fill="#F3F3F3"], [fill="#ffffff"], [fill="#FFFFFF"], [fill="white"]').addClass(
            'svg-fill-bg');
          $svg.find('[fill="#41ccb4"], [fill="#41CCB4"]').addClass('svg-fill-primary');
          $svg.find('[fill="#142533"]').addClass('svg-fill-secondary');
          $svg.find('[stroke="#f3f3f3"], [stroke="#F3F3F3"], [stroke="#ffffff"], [stroke="#FFFFFF"]').addClass(
            'svg-stroke-bg');
          $svg.find('[stroke="#41ccb4"], [stroke="#41CCB"]').addClass('svg-stroke-primary');
          $svg.find('[stroke="#142533"]').addClass('svg-stroke-secondary');
          $svg.attr('class', this.$container.attr('class')).removeClass('is-hidden');
          this.$container.after($svg);
          this.$container.remove();
        }.bind(this));
      }
      else {
        this.$container.attr('src', window.theme.settings.icons[sectionId]).removeClass('is-hidden');
      }
    }
  }
});

$$.jqueryPlugin('linkToFirstArticle', {
  defaultOptions: {
    sectionId: 0,
    data: null
  },
  methods: {
    init: function(uid, options, $container) {

      this.options = options;
      this.$container = $container;
      this.data = options.data;

      if (!this.data) {
        $(window).on('apiDataFetched', function(event, data) {
          this.data = data;
          this.setLinkUrl();
        }.bind(this));
      }
      else {
        this.setLinkUrl();
      }

    },

    getArticleUrl: function() {
      var childSection = this.data.sections.filter(function(section) {
        return section.parent_section_id === this.options.sectionId;
      }.bind(this))[0];

      var article = this.data.articles
        .filter(function(article) {
          return (childSection ? article.section_id === childSection : article.section_id === this.options.sectionId) && !article.position;
        }.bind(this))[0];

      return article ? article.html_url : null;
    },

    setLinkUrl: function() {
      var articleUrl = this.getArticleUrl();

      if (articleUrl) {
        this.$container.attr('href', articleUrl);
      }
    }
  }
});

$$.jqueryPlugin('crypto', {
  defaultOptions: {
    sectionId: 0,
    eventRender: 'crypto:render'
  },
  methods: {
    init: function(uid, options, $container) {
      this.options = options;
      this.$container = $container;

      this.getCoinData();
      this.bindEvents();
    },

    bindEvents: function() {
      $(window).on('apiDataFetched', this.handleApiDataFetched.bind(this));
    },

    handleApiDataFetched: function(event, data) {
      this.data = data;
      this.sections = data.sections.filter(function(section) {
        return section.parent_section_id === this.options.sectionId;
      }.bind(this));

      this.sortAndRender();
    },

    handleCoinmarketcapDataFetched: function(response) {
      this.coinData = typeof response === 'string' ? JSON.parse(response).data : response.data;
      this.sortAndRender();

      localStorage.setItem('coin:data:endtime', (Date.now() + (1000 * 60 * 60)).toString());
      localStorage.setItem('coin:data', JSON.stringify(this.coinData));
    },

    getCoinData: function() {
      var endtime = localStorage.getItem('coin:data:endtime');
      var coinData = localStorage.getItem('coin:data');

      if (coinData && endtime && parseInt(endtime) > Date.now()) {
        this.coinData = JSON.parse(coinData);
        this.sortAndRender();
      }
      else {
        $.get('https://coinmarketcap-api.netlify.app/.netlify/functions/api?v=4')
          .done(this.handleCoinmarketcapDataFetched.bind(this))
          .fail(this.renderWithoutSort.bind(this));
      }
    },

    sortAndRender: function() {
      if (this.sections && this.coinData) {
        this.$container.html('');
        this.sections
          .map(this.sectionsMap.bind(this))
          .sort(this.sectionsSort.bind(this))
          .forEach(this.render.bind(this));

        this.$container.trigger(this.options.eventRender, this);
      }
    },

    renderWithoutSort: function() {
      this.$container.html('');
      this.sections.forEach(this.render.bind(this));
      this.$container.trigger(this.options.eventRender, this);
    },

    sectionsMap: function(section) {
      var symbolPattern = /^[\S\s]+\(([\S\s]+)\)[\S\s]*$/;
      var symbol = symbolPattern.test(section.name) ? section.name.replace(symbolPattern, '$1') : null;

      section.index = this.coinData.findIndex(function(item) {
        var isCurrent = symbol ? item.symbol === symbol : section.name.indexOf(item.name) !== -1;
        if (isCurrent) section.coinId = item.id;
        return isCurrent;
      });

      return section;
    },

    sectionsSort: function(a, b) {
      return a.index === -1 ? 1
        : a.index < b.index ? -1
          : a.index > b.index ? 1
            : 0;
    },

    render: function(section) {
      this.$container.append('\
          <div class="crypto-item" data-section-id="' + section.id + '">\
            <a href="' + section.html_url + '">\
              ' + this.getCoinIcon(section) + '\
              <div class="crypto-item__name">' + section.name.replace('(', '<i>').replace(')', '</i>') + '</div>\
            </a>\
          </div>\
        ');

      this.$container.find('[data-section-id]').each(function(i, element) {
        var $element = $(element);
        var sectionId = $element.data('section-id');
        $element.find('a').linkToFirstArticle({sectionId: sectionId, data: this.data});
      }.bind(this));
    },

    getCoinIcon: function(section) {
      return window.theme.settings.icons[section.id]
        ? '<div class="crypto-item__icon"><img src="' + window.theme.settings.icons[section.id] + '" alt="' + section.name + '"></div>'
        : '<div class="crypto-item__icon"><i>?</i></div>';
    }
  }
});

$$.jqueryPlugin('cryptoSearch', {
  defaultOptions: {
    minSymbols: 1
  },
  methods: {
    init: function(uid, options, $container) {
      this.options = options;

      this.$form = $container;
      this.$input = $container.find('input[type="search"]');
      this.$cryptoContainer = $('[data-plugin-crypto]');

      this.bindEvents();
    },

    bindEvents: function() {
      this.$cryptoContainer.on('crypto:render', this.handleCryptoRender.bind(this));
      this.$input.on('input', this.handleInput.bind(this));
      this.$form.on('submit', this.handleFormSubmit.bind(this));
    },

    handleCryptoRender: function(event, cryptoPlugin) {
      this.sections = cryptoPlugin.sections;
      this.$items = this.$cryptoContainer.find('[data-section-id]');
    },

    handleInput: function() {
      if (this.sections) {
        this.filter();
      }
    },

    handleFormSubmit: function(event) {
      event.preventDefault();
      if (this.sections) {
        this.filter();
      }
    },

    filter: function() {
      var value = this.$input.val();

      if (value.length >= this.options.minSymbols) {
        var patternStr = '';
        for (var i = 0; i < value.length; i++) {
          var symbol = value.charAt(i);
          patternStr += '[' + symbol.toLowerCase() + symbol.toUpperCase() + ']';
        }
        var pattern = new RegExp(patternStr, 'i');
        var sections = this.sections.filter(function(section) {
          return pattern.test(section.name);
        });

        this.$items.hide();

        sections.forEach(function(section) {
          this.$cryptoContainer.find('[data-section-id="' + section.id + '"]').show();
        }.bind(this));
      }
      else {
        this.$items.show();
      }
    }
  }
});

$$.jqueryPlugin('headerSearch', {
  methods: {
    init: function(uid, options, $container) {
      if ($('[type="search"]').length === 1 && !window.hideSearchBar) {
        $container.removeClass('is-hidden');
      }
    }
  }
});

$$.jqueryPlugin('supportSections', {
  defaultOptions: {
    sidebarSelector: '.js-sections-sidebar',
    contentSelector: '.js-sections-content',
    sidenavSelector: '.js-sidenav',
    sidenavToggleSelector: '.js-sidenav-toggle',
    articleId: 0
  },
  methods: {
    init: function(uid, options, $container) {
      this.options = options;

      this.$container = $container;
      this.$sidebar = $container.find(options.sidebarSelector);
      this.$content = $container.find(options.contentSelector);
      this.$sidenav = $container.find(options.sidenavSelector);
      this.$sidenavToggle = $container.find(options.sidenavToggleSelector);
      this.breadcrumbs = [];
      this.parentSections = [];

      this.bindEvents();
    },

    bindEvents: function() {
      $(window).on('apiDataFetched', this.handleApiDataFetched.bind(this));
      this.$sidenavToggle.on('click', this.handleSidenavToggle.bind(this));
    },

    handleSidenavToggle: function(event) {
      event.preventDefault();
      this.$sidenav.toggleClass('is-active');
    },

    handleApiDataFetched: function(event, data) {
      this.data = data;
      this.setBreadcrumbs();
      this.render();

      if (this.$content.length) {
        var tabActiveIndex = this.parentSections.findIndex(function(section) {
          return section.id === this.breadcrumbs[1];
        }.bind(this));

        this.$container.tabs({
          linksSelector: this.options.sidebarSelector + ' .sidebar__headings',
          containersSelector: this.options.contentSelector,
          activeIndex: tabActiveIndex !== -1 ? tabActiveIndex : 0
        });

        this.$container.on('tabs:opened', function() {
          var text = $(this.options.sidebarSelector + ' .sidebar__headings .is-active').text();
          $(this.options.sidebarSelector + ' .sidebar__active-heading').text(text);
          $(this.options.sidebarSelector + ' [data-plugin-spoiler]').spoiler('close');
        }.bind(this));
      }

      this.$container.find('[data-plugin-spoiler]').each(function(i, element) {
        var $element = $(element);
        var options = $element.data('plugin-spoiler');
        $element.spoiler(options);
      });

      if (window.innerWidth < 768) {
        $('html, body').animate({
          scrollTop: $('.content__body').offset().top
        });
      }
    },

    setBreadcrumbs: function() {
      var sectionPattern = /^[\S\s]+\/sections\/([0-9]+)[\S\s]+$/;

      $('.breadcrumbs > li').each(function(i, element) {
        var url = $(element).find('a').attr('href');

        if (i && sectionPattern.test(url)) {
          var sectionId = parseInt(url.replace(sectionPattern, '$1'));
          this.breadcrumbs.push(sectionId);

          if (this.breadcrumbs.length === 1) {
            this.parentSections = this.data.sections.filter(function(section) {
              return section.parent_section_id === sectionId;
            });
          }
        }
      }.bind(this));
    },

    render: function() {
      var sidebarHtml = '';
      var contentHtml = '';
      var isTabs = !!this.$content.length;

      if (!this.$content.length) {
        sidebarHtml += '<h1 class="sidebar__heading">' + $('.breadcrumbs > li').eq(2).text() + '</h1>';
      }

      if ($$.page.isArticlePage()) {
        var index = -1;
        var firstSection = null;

        this.parentSections.forEach(function(section, i) {
          if (section.id === this.breadcrumbs[1]) {
            index = i;
            firstSection = Object.assign({}, section);
          }
        }.bind(this));

        if (firstSection) {
          this.parentSections.splice(index, 1);
          this.parentSections.splice(0, 0, firstSection);
        }
      }

      var activeSection = !isTabs
        ? this.parentSections.filter(function(section) {return section.id === this.breadcrumbs[1];}.bind(this))[0]
        : this.parentSections[0];

      if (this.$content.length) {
        sidebarHtml += '<div class="sidebar__container" data-plugin-spoiler>';
        sidebarHtml += '<div class="sidebar__active-heading js-spoiler-toggle">' + activeSection.name + '</div>';
        sidebarHtml += '<div class="sidebar__headings js-spoiler">';
      }

      this.parentSections.forEach(function(section, index) {
        var url = isTabs ? '#' + section.id : section.html_url;
        var isActive = !isTabs && section.id === this.breadcrumbs[1];

        if (this.$content.length) {
          sidebarHtml += '<a href="' + url + '" class="sidebar__item ' + (isActive ? 'is-active' : '') + '" tabindex="0">';
          sidebarHtml += '<span>' + section.name + '</span>';
          sidebarHtml += '</a>';

          contentHtml += '<div id="' + section.id + '" class="body__content">';
          contentHtml += '<div class="body__heading">';
          contentHtml += '<div class="body__heading-title body__heading-title--' + section.id + '">';
          contentHtml += '<h3>' + section.name + '</h3>';
          contentHtml += section.description ? '<p>' + section.description + '</p>' : '';
          contentHtml += '</div>';

          contentHtml += '</div>';
          contentHtml += '<div class="section-link__items">' + this.getSectionsTemplate(section.id) + '</div>';
          contentHtml += '</div>';
        }
        else {
          if (this.parentSections.length > 10 && index === 10) {
            sidebarHtml += '<div class="spoiler spoiler--type-1 spoiler--style-2" data-plugin-spoiler>\
              <a class="spoiler__title js-spoiler-toggle sidebar__show-more" role="button" tabindex="0"></a>\
              <div class="spoiler__text js-spoiler" aria-hidden="true">';
          }

          sidebarHtml += this.getSidenavTemplate(section, isActive, 1);

          if (this.parentSections.length > 10 && index === (this.parentSections.length - 1)) {
            sidebarHtml += '</div></div>';
          }
        }
      }.bind(this));

      if (this.$content.length) {
        sidebarHtml += '</div></div>';
      }

      if (this.parentSections.length) {
        this.data.articles.forEach(function(article) {
          if (article.section_id === this.parentSections[0].parent_section_id) {
            var isActive = (this.options.articleId && this.options.articleId === article.id) ? 'is-active' : '';
            sidebarHtml += '<a href="' + article.html_url + '" class="sidebar__item sidebar__item--article ' + isActive + '">' + article.title + '</a>';
          }
        }.bind(this));
      }

      this.$sidebar.html(sidebarHtml);

      if (this.$content.length) {
        this.$content.html(contentHtml);
      }
    },

    getSidenavTemplate: function(parentSection, isActive, i) {
      var sidebarHtml = '';
      sidebarHtml += '<div data-plugin-spoiler=\'{"open":' + isActive + '}\'>';
      sidebarHtml += '<a class="sidebar__item js-spoiler-toggle" role="button" tabindex="0">' + parentSection.name + '</a>';
      sidebarHtml += '<div class="sidebar__item-body js-spoiler" aria-hidden="true">';

      this.data.sections.forEach(function(section) {
        if (section.parent_section_id === parentSection.id) {
          var isActive = section.id === this.breadcrumbs[i + 1];
          sidebarHtml += this.getSidenavTemplate(section, isActive, (i + 1));
        }
      }.bind(this));

      this.data.articles
        .filter(function(article) {
          return article.section_id === parentSection.id;
        })
        .sort(function(a, b) {
          return a.position - b.position;
        })
        .forEach(function(article) {
          var isActive = (this.options.articleId && this.options.articleId === article.id) ? 'is-active' : '';
          sidebarHtml += '<a href="' + article.html_url + '" class="sidebar__article ' + isActive + '">' + article.title + '</a>';
        }.bind(this));

      sidebarHtml += '</div>';
      sidebarHtml += '</div>';

      return sidebarHtml;
    },

    getSectionsTemplate: function(sectionId) {
      var html = '';
      var articlesHtml = '';

      this.data.sections.forEach(function(section) {
        var isOpen = this.breadcrumbs[2] ? this.breadcrumbs[2] === section.id : !html;
        if (section.parent_section_id === sectionId) {
          html += '<div class="section-list__item" data-plugin-spoiler=\'{"open":' + isOpen + '}\'>';
          html += '<a class="section-list__item-title js-spoiler-toggle" role="button" tabindex="0">' + section.name + '</a>';
          html += '<div class="section-list__item-body js-spoiler" aria-hidden="true">';
          html += this.getArticlesTemplate(section);
          html += '</div>';
          html += '</div>';
        }
      }.bind(this));

      this.data.articles.forEach(function(article) {
        if (article.section_id === sectionId) {
          articlesHtml += '<div class="promoted__item">';
          articlesHtml += '<a href="' + article.html_url + '" class="more-link"><span>' + article.title + '</span>';
          articlesHtml += '<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">\n' +
            '<path d="M14.7236 18.624L21.3716 12L14.7236 5.37604L13.5236 6.55204L16.9076 9.91204C17.2916 10.296 17.7236 10.704 18.1556 11.088H2.62762V12.912H18.1556C17.7236 13.296 17.2916 13.704 16.9076 14.088L13.5236 17.448L14.7236 18.624Z" fill="black"/>\n' +
            '</svg></a>';
          articlesHtml += '</div>';
        }
      });

      if (html && articlesHtml) {
        html += '<div class="section-list__item-separator"></div>';
      }

      return html + articlesHtml;
    },

    getArticlesTemplate: function(section) {
      var html = '';

      this.data.articles
        .filter(function(article) {
          return article.section_id === section.id;
        })
        .sort(function(a, b) {
          return a.position - b.position;
        })
        .forEach(function(article) {
          html += '<div class="promoted__item">';
          html += '<a href="' + article.html_url + '" class="more-link"><span>' + article.title + '</span>';
          html += '<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">\n' +
            '<path d="M14.7236 18.624L21.3716 12L14.7236 5.37604L13.5236 6.55204L16.9076 9.91204C17.2916 10.296 17.7236 10.704 18.1556 11.088H2.62762V12.912H18.1556C17.7236 13.296 17.2916 13.704 16.9076 14.088L13.5236 17.448L14.7236 18.624Z" fill="black"/>\n' +
            '</svg></a>';
          html += '</div>';
        });

      return html;
    }
  }
});

$$.jqueryPlugin('toc', {
  defaultOptions: {

    // Heading selectors
    headings: 'h1, h2, .h1, .h2',

    // Flat toc or tree
    flat: false,

    // The toc title
    title: '',

    // Show the container if headings not found
    showEmpty: false
  },
  methods: {
    /**
     * Plugin initialization
     * @param {Number} uid - unique plugin ID
     * @param {Object} options
     * @param {jQuery} $container
     */
    init: function(uid, options, $container) {
      this.uid = uid;
      this.options = options;
      this.elementList = [];
      this.elementTree = [];
      this.uperLevel = 6;

      this.$window = $(window);
      this.$container = $container;
      this.$article = $('[data-article-body]');
      this.$headings = this.$article.find(this.options.headings);
      this.rtl = $('html').attr('dir') === 'rtl';

      if (!this.$article.length) return;

      this.lastElement = null;
      this.$headings.each(this.createTocElement.bind(this));
      this.createTree();

      if (this.elementTree.length || this.options.showEmpty) {
        this.$container.addClass('toc');
        this.createToc();
        this.setVariables();
        this.updateContainerWidth();
        this.handleWindowScroll();
        this.bindEvents();
        this.setVisibleItems();
      }
      else {
        this.$container.html('');
      }
    },

    setVisibleItems: function() {
      setTimeout(function() {
        var $tocElements = $('[data-toc-element-id]');
        $tocElements.addClass('is-hidden');
        this.$headings.filter(':visible').each(function(i, element) {
          $('[data-toc-element-id="' + $(element).data('id') + '"]').removeClass('is-hidden');
        });
      }.bind(this), 1);
    },

    /**
     * Set variables after toc render
     */
    setVariables: function() {
      this.offsetTop = this.getOffsetTop();
      this.$tocContainer = this.$container.find('.js-toc-container');
      this.$parentList = this.$tocContainer.find('.js-toc-list').first();
      this.tocPosition = 'top';
      this.lastActiveId = null;
      this.activeId = null;
      this.tocIsVisible = this.$container[0].offsetParent != null;
    },

    /**
     * Bind events
     */
    bindEvents: function() {
      var click = 'click.' + this.uid + ' enterkey.' + this.uid;

      this.$window.on('resize.' + this.uid, this.updateContainerWidth.bind(this));
      this.$window.on('scroll.' + this.uid, this.handleWindowScroll.bind(this));
      this.$container.on(click, 'a', this.handleLinkClick.bind(this));
      $('[data-article-body]').on('click', '.tabs-link', this.setVisibleItems.bind(this));
    },

    /**
     * Handle link click
     * Scroll the window to the heading
     * @param {Event} event
     */
    handleLinkClick: function(event) {
      var $link = $(event.currentTarget);
      var headingId = $link.attr('href').replace('#', '');

      if (headingId) {
        event.preventDefault();
        var $heading = this.$article.find('[data-id="' + headingId + '"]');
        var top = $heading.offset().top - this.offsetTop;
        $('html, body').animate({scrollTop: top}, 300);
      }
    },

    /**
     * Handle window scroll
     * Sets the position of the toc container
     * Finds the active link of the toc
     */
    handleWindowScroll: function() {
      if (this.tocIsVisible) {
        var containerRect = this.$container[0].getBoundingClientRect();

        if (containerRect.top <= this.offsetTop) {
          var tocContainerRect = this.$tocContainer[0].getBoundingClientRect();
          if (containerRect.bottom >= tocContainerRect.bottom && tocContainerRect.top >= this.offsetTop) {
            if (this.tocPosition !== 'center') {
              this.tocPosition = 'center';
              this.$tocContainer
                .removeClass('is-bottom')
                .addClass('is-fixed')
                .css('top', this.offsetTop);
            }
          }
          else if (this.tocPosition !== 'bottom') {
            this.tocPosition = 'bottom';
            this.$tocContainer
              .removeClass('is-fixed')
              .addClass('is-bottom')
              [0].style.top = null;
          }

        }
        else if (this.tocPosition !== 'top') {
          this.tocPosition = 'top';
          this.$tocContainer
            .removeClass('is-fixed is-bottom')
            [0].style.top = null;
        }

        for (var i = 0; i < this.$headings.length; i++) {
          var rect = this.$headings[i].getBoundingClientRect();
          if (rect.top > this.offsetTop + 1) {
            this.lastActiveId = this.activeId;
            this.activeId = i
              ? this.$headings.eq(i - 1).data('id')
              : null;
            break;
          }
          if (i === this.$headings.length - 1) {
            this.lastActiveId = this.activeId;
            this.activeId = this.$headings.eq(i).data('id');
          }
        }

        if (!this.activeId) {
          this.activeId = this.$headings.eq(0).data('id');
        }

        if (this.lastActiveId !== this.activeId) {
          if (this.lastActiveId) {
            this.$container
              .find('[data-toc-element-id="' + this.lastActiveId + '"]')
              .removeClass('is-active');
          }
          if (this.activeId) {
            var $element = this.$container.find('[data-toc-element-id="' + this.activeId + '"]');
            var elementRect = $element[0].getBoundingClientRect();
            var parentListRect = this.$parentList[0].getBoundingClientRect();
            $element.addClass('is-active');
            this.$parentList.animate(
              {scrollTop: elementRect.top - parentListRect.top - parentListRect.height / 2}, 50);
          }
        }
      }
    },

    /**
     * Returns the top offset of the toc container
     * @return {number}
     */
    getOffsetTop: function() {
      var $header = $('[data-plugin-header]');
      var mainTop = $('main[role="main"]').offset().top;

      return getComputedStyle($header[0])['position'] !== 'fixed'
        ? mainTop - $header.innerHeight() + 12
        : mainTop + 12;
    },

    /**
     * Creates a toc element and pushes them to the
     * "this.elementList" array
     * @param {number} ind
     * @param {HTMLElement} element
     */
    createTocElement: function(ind, element) {
      var $heading = $(element);

      var heading = {
        id: $$.createUID(),
        title: $heading.text(),
        level: this.getHeadingLevel($heading),
        parentId: null,
        children: []
      };

      if (this.uperLevel > heading.level) {
        this.uperLevel = heading.level;
      }

      if (this.elementList.length && !this.options.flat && heading.level > this.lastElement.level) {
        heading.parentId = this.lastElement.id;
      }

      if (!heading.parentId) {
        this.lastElement = heading;
      }

      this.elementList.push(heading);

      $heading.attr('data-id', heading.id);

    },

    /**
     * Creates toc element tree
     * from the toc element list
     */
    createTree: function() {
      if (this.options.flat) {
        return this.elementTree = this.elementList;
      }

      this.elementTree = this.elementList
        .filter(function(element) {
          return !element.parentId;
        })
        .map(function(element) {
          element.children = this.elementList.filter(function(element2) {
            return element2.parentId === element.id;
          });

          return element;
        }.bind(this));
    },

    /**
     * Creates toc html and renders them
     * on the page
     */
    createToc: function() {
      var html = this.tocWrapperTemplate(
        this.options.title,
        this.tocContainerTemplate(
          this.elementTree.map(function(element) {
            var children = element.children.length
              ? this.tocContainerTemplate(
                element.children.map(function(element) {
                  return this.tocElementTemplate(element.level - this.uperLevel, element, '');
                }.bind(this)).join('')
              )
              : '';

            return this.tocElementTemplate(
              element.level - this.uperLevel,
              element,
              children
            );
          }.bind(this)).join('')
        )
      );

      this.$container.html(html);
    },

    /**
     * Returns a heading level number
     * @param {jQuery} $heading
     * @return {number}
     */
    getHeadingLevel: function($heading) {
      var nodeNamePattern = /H1|H2|H3|H4|H5|H6/;
      var classNamePattern = /^([\s\S]*\s)?(h1|h2|h3|h4|h5|h6)(\s[\s\S]*)?$/;

      if (nodeNamePattern.test($heading[0].nodeName)) {
        return parseInt($heading[0].nodeName.replace('H', ''));
      }

      if (classNamePattern.test($heading.attr('class'))) {
        return parseInt($heading.attr('class').replace(classNamePattern, '$2').replace('h', ''));
      }

      return 6;
    },

    /**
     * Updates width and height of the container
     * @param {ClientRect?} containerRect
     */
    updateContainerWidth: function(containerRect) {
      this.tocIsVisible = this.$container[0].offsetParent != null;

      if (this.tocIsVisible) {
        this.offsetTop = this.getOffsetTop();

        containerRect = typeof containerRect === 'number'
          ? containerRect
          : this.$container[0].getBoundingClientRect();

        var width = this.rtl
          ? containerRect.right
          : window.innerWidth - containerRect.left - 10

        this.$tocContainer.css({
          'width': width,
          'max-height': window.innerHeight - this.offsetTop - 10
        });
      }
    },

    tocWrapperTemplate: function(title, children) {
      return '\
          <div class="toc__container js-toc-container">\
            <h4 class="toc__title">' + title + '</h4>\
            ' + children + '\
          </div>\
      ';
    },

    tocContainerTemplate: function(children) {
      return '<ul class="toc__list js-toc-list">' + children + '</ul>';
    },

    tocElementTemplate: function(level, element, children) {
      return '\
          <li class="is-hidden toc__element toc__element--level-' + level + '" data-toc-element-id="' + element.id + '">\
              <a href="#' + element.id + '">' + element.title + '</a>\
              ' + children + '\
          </li>\
      ';
    }
  }
});

$$.jqueryPlugin('glossary', {
  methods: {
    init: function(uid, options, $container) {
      this.$container = $container;
      this.$table = $container.find('table').first();
      this.symbols = [];

      this.fetchSymbols();
      this.renderNavigation();
      this.firstAnimate();

      this.bindEvents();
    },

    bindEvents: function() {
      this.$container.on('click', '.js-glossary-link', this.handleLinkClick.bind(this));
    },

    fetchSymbols: function() {
      this.$table.find('td').each(function(i, element) {
        var $element = $(element);
        var $heading = $element.find('h1, h2, h3, h4, h5').first();
        var symbol = $heading.text().substr(0, 1).toLowerCase();

        if (/[0-9]/.test(symbol)) {
          symbol = '0-9';
        }

        $element.attr('id', symbol);

        if (this.symbols.indexOf(symbol) === -1) {
          this.symbols.push(symbol);
        }
      }.bind(this));
    },

    renderNavigation: function() {
      var html = '<ul class="glossary__symbols">';

      this.symbols.forEach(function(symbol) {
        html += '<li><a href="#' + symbol + '" class="js-glossary-link">' + symbol.toUpperCase() + '</a></li>';
      });

      html += '</ul>';

      this.$table.before(html);
    },

    firstAnimate: function() {
      setTimeout(function() {
        if (window.location.hash) {
          var $element = this.$table.find(window.location.hash).first();

          if ($element) {
            $('html, body').animate({scrollTop: $element.offset().top - 40}, 300);
          }
        }
      }.bind(this), 300);
    },

    handleLinkClick: function(event) {
      event.preventDefault();
      var id = $(event.currentTarget).attr('href');
      var $element = this.$table.find(id).first();
      $('html, body').animate({scrollTop: $element.offset().top - 40}, 300);
      history.replaceState(null, null, id);
    }
  }
});

$$.jqueryPlugin('image', {
  methods: {
    init: function(uid, options, $container) {
      this.$conatiner = $container;
      this.$body = $('body');
      this.bindEvents();
    },

    bindEvents: function() {
      this.$conatiner.on('click', 'img', this.handleImageClick.bind(this));
      this.$body.on('click', '.image-lightbox', this.handleImageClose.bind(this));
    },

    handleImageClick: function(event) {
      event.preventDefault();
      var $image = $(event.currentTarget);
      var src = $image.attr('src');

      this.$body.append('<div class="image-lightbox">\
          <a href="#" class="image-lightbox__close"><i class="fas fa-times"></i></a>\
          <img src="' + src + '" alt=""/>\
        </div>');
    },

    handleImageClose: function(event) {
      event.preventDefault();
      $(event.currentTarget).remove();
    }
  }
});

$$.jqueryPlugin('to-top', {
  methods: {
    init: function(uid, options, $container) {
      this.$container = $container;

      this.$container.on('click', this.handleScrollToTop.bind(this));
      $(window).on('scroll.' + uid, this.handleWindowScroll.bind(this));

      this.handleWindowScroll();
    },

    handleScrollToTop: function(event) {
      event.preventDefault();
      $('html, body').animate({scrollTop: 0}, 500);
    },

    handleWindowScroll: function() {
      if (window.scrollY > 200 && this.$container.hasClass('is-hidden')) {
        this.$container.removeClass('is-hidden');
      }
      else if (window.scrollY <= 200 && !this.$container.hasClass('is-hidden')) {
        this.$container.addClass('is-hidden');
      }
    }
  }
});

$$.jqueryPlugin('fix-autocomplate', {
  methods: {
    init: function(uid, options, $container) {
      this.search = $container.find('input[type="search"]');

      var observerTarget = $('body')[0];
      var observerConfig = {
        attributes: false,
        childList: true,
        subtree: false
      };

      var observer = new MutationObserver(this.handleMutation.bind(this));
      observer.observe(observerTarget, observerConfig);
    },

    handleMutation: function(mutationsList) {
      mutationsList.forEach(function(mutation) {
        mutation.addedNodes.forEach(function(node) {
          if (node.nodeName === 'ZD-AUTOCOMPLETE' && this.search.is(':focus') && node.style.top.indexOf('-') === 0) {
            node.style.top = this.search.offset().top + this.search.innerHeight() + 10 + 'px';
          }
        }.bind(this));
      }.bind(this));
    }
  }
});

$$.jqueryPlugin('language-swither', {
  defaultOptions: {
    defaultLanguage: 'en-us',
    enableLanguages: ''
  },
  methods: {
    init: function(uid, options, $container) {
      this.options = options;
      this.$container = $container;
      this.lenguages = options.enableLanguages.split(/,[\s]*/);

      if (this.lenguages.indexOf(this.options.defaultLanguage) === -1) {
        this.lenguages.push(this.options.defaultLanguage);
      }

      if (window.HelpCenter.user.role === 'anonymous' || window.HelpCenter.user.role === 'end_user') {
        this.languageControl();
      }
      else {
        $container.removeClass('is-hidden');
      }
    },

    languageControl: function() {
      // var locale = new RegExp('hc\\/' + window.theme.locale + '[^A-Za-z0-9_?&-]?');
      var locale = new RegExp('hc\\/' + window.theme.locale);
      var defaultLocale = 'hc/' + this.options.defaultLanguage;

      if (window.theme.locale !== this.options.defaultLanguage && this.lenguages.indexOf(window.theme.locale) === -1) {
        var newUrl = window.location.href.replace(locale, defaultLocale);
        window.location.replace(newUrl);
      }

      this.$container.find('[role="menuitem"]').each(function(i, element) {
        var $element = $(element);

        this.lenguages.forEach(function(language) {
          var languagePath = '/' + language + '?';
          if ($element.attr('href').indexOf(languagePath) !== -1) {
            $element.attr('is-active', true);
          }
        }.bind(this));

        if (!$element.attr('is-active')) {
          $element.remove();
        }
      }.bind(this));

      if (!this.$container.find('[role="menuitem"]').length) {
        this.$container.remove();
      }
      else {
        this.$container.removeClass('is-hidden');
      }

    }
  }
});

$$.jqueryPlugin('short-title', {
  methods: {
    init: function(uid, options, $container) {
      var $title = $container.find('.js-article-title');
      var $body = $container.find('.js-article-body');
      var $shortTitle = $body.find('.art-alt-name');

      var title = $shortTitle.length ? $shortTitle.text() : $title.text();

      $title.text(title);
      $body.remove();
      $container.removeClass('is-hidden');
    }
  }
});

$$.jqueryPlugin('locale-name', {
  methods: {
    init: function(uid, options, $container) {
      $container
        .text($container.text().replace(/^([^(]+)[\s\S]*$/, '$1'))
        .removeClass('is-hidden');
    }
  }
});

$$.jqueryPlugin('header', {
  defaultOptions: {
    searchContainer: '.search-container',
    menuContainer: '.header__right',
    siteOverlay: '.site-overlay',
    searchToggle: '.js-search-toggle',
    menuToggle: '.js-menu-toggle'
  },
  methods: {
    init: function(uid, options, $container) {
      this.options = options;
      this.$container = $container;
      this.bindEvents();
    },

    bindEvents: function() {
      this.$container.on('click', this.options.searchToggle, this.handleSearchToggle.bind(this));
      this.$container.on('click', this.options.menuToggle, this.handleMenuToggle.bind(this));
    },

    handleSearchToggle: function(event) {
      event.preventDefault();
      $(this.options.searchContainer).toggleClass('is-active');
    },

    handleMenuToggle: function(event) {
      event.preventDefault();
      $(this.options.menuContainer).slideToggle(300);
      $(this.options.siteOverlay).toggleClass('is-active');
    }
  }
});

$$.jqueryPlugin('footer', {
  methods: {
    init: function(uid, opts, $container) {
      $container.on('click', '.footer__menu-heading', function(event) {
        event.preventDefault();

        var $element = $(event.currentTarget);
        var $menu = $element.next('.footer__menu-list').first();

        $element.toggleClass('is-active');
        $menu.slideToggle(300);
      });
    }
  }
});

$$.jqueryPlugin('related-articles', {
  defaultOptions: {
    icons: ''
  },
  methods: {
    init: function(uid, opts, $container) {
      this.options = opts;

      $container.find('a').each(function(i, element) {
        var $element = $(element);
        var url = $element.attr('href');
        var text = $element.text();

        $element
          .closest('li')
          .addClass('promoted__item')
          .html(this.template(url, text));
      }.bind(this));
    },

    template: function(url, title) {
      return '<a href="' + url + '" class="more-link">\
          <span>' + title + '</span>\
          <img src="' + this.options.icon + '" alt="">\
        </a>';
    }
  }
});

$$.jqueryPlugin('support-title', {
  methods: {
    init: function(uid, opts, $container) {
      $container
        .html($container.text().replace(/([.。,،]\s*)/, '$1<br/>'));
    }
  }
});

$$.jqueryPlugin('sections-description', {
  methods: {
    init: function(uid, opts, $container) {
      $container
        .html($container.text().replace(/(Nano\s+[XS])/, '<span style="display: inline-block">$1</span>'));
    }
  }
});

// P+SC
$$.jqueryPlugin('language-suggestion', {
  defaultOptions: {
    switcher: 'a.switch',
    alternatives: 'span.alternatives',
    cancel_button: 'a.cancel',
    url_param_cancel: 'language-suggestion'
  },
  methods: {
    init: function(uid, opts, $container) {
      var container = $container[0];

      if (location.search.indexOf('&' + opts.url_param_cancel) > -1) {
        localStorage.setItem(opts.url_param_cancel, 'true');
      }

      var cancel = localStorage.getItem(opts.url_param_cancel);
      if (cancel) {
        return;
      }


      // var switchers_el = container.querySelector(opts.switchers);
      var switcher_el = container.querySelector(opts.switcher);
      var alternatives_els = container.querySelectorAll(opts.alternatives);
      var cancel_button_els = container.querySelectorAll(opts.cancel_button);

      // f.e. [{'name': 'English (US)', 'url': '/hc/change_language/en-us?return_to=...'}]
      var alternative_locales = JSON.parse(container.getAttribute('data-alternative_locales') || '[]');
      var alternative_locales_by_lang = alternative_locales
        // .map(function(locale) {
        // locale.name = locale.name.split(' (')[0];  // f.e. "Français (France)" -> "Français"
        // return locale;
        // })
        .reduce(function(obj, locale) {
          var lang = locale.url.split('?')[0].split('/').slice(-1)[0];
          obj[lang] = locale;
          return obj;
        }, {});

      var matching_lang = getMatchingLanguage(window.navigator.languages, Object.keys(alternative_locales_by_lang));

      if (
        !matching_lang ||
        (window.theme.settings.languageSwitcherControl &&
          window.theme.settings.enableLanguages.indexOf(matching_lang) === -1)
      ) {
        return;
      }

      container.classList.remove('hidden');

      // var matching_langs_names = matching_langs.map(function(lang) {
      //   var locale = alternative_locales_by_lang[lang];
      //   return getLanguageInEnglish(lang) || locale.name;
      // });
      //alternatives_els.forEach(function(el) { el.textContent = joinItems(matching_langs_names); });
      alternatives_els.forEach(function(el) { el.textContent = getLanguageInEnglish(matching_lang); });

      // matching_langs.forEach(function(lang, i) {
      //   var locale = alternative_locales_by_lang[lang];
      //   var li = document.createElement('li');
      // 	var a = document.createElement('a');
      //   a.textContent = getLanguageInEnglish(lang) || locale.name;
      //   a.href = locale.url + encodeURIComponent('&' + opts.url_param_cancel);
      //   li.appendChild(a);
      //   switchers_el.appendChild(li);
      // });
      var locale = alternative_locales_by_lang[matching_lang];
      switcher_el.href = locale.url + encodeURIComponent('&' + opts.url_param_cancel);

      switcher_el.addEventListener('click', function() {
        localStorage.setItem(opts.url_param_cancel, 'true');
      });

      cancel_button_els.forEach(function(el) {
        el.addEventListener('click', function(e) {
          e.preventDefault();
          container.classList.add('hidden');
          localStorage.setItem(opts.url_param_cancel, 'true');
        });
      });


      function getMatchingLanguage(a, b) {
        a = a.map(toLowerCase);
        b = b.map(toLowerCase);
        // console.log(a, b);
        var exact_matches = a.filter(function(item) { b.indexOf(item) > -1; });
        var a_langs = a.map(langFromLocale);
        var lang_matches = b
          .filter(function(locale) {
            var lang = langFromLocale(locale);
            return a_langs.indexOf(lang) > -1;
          })
          .filter(outDuplicates);
        var matches = exact_matches
          .concat(lang_matches)
          .filter(outDuplicates);
        return matches[0];  // new: just do one language option
      }

      function toLowerCase(item) { return item.toLowerCase(); }

      function langFromLocale(locale) { return locale.split('-')[0]; }

      function outDuplicates(item, i, arr) {
        return arr.indexOf(item) == i;
      }

      // function joinItems(items) {
      //   if (items.length < 2) { return items[0] };
      //   return items.slice(0, -1).join(', ') + ' & ' + items.slice(-1)[0];
      // }

      function getLanguageInEnglish(language) {
        var languages = {
          'ab': 'Abkhazian',
          'ace': 'Achinese',
          'ach': 'Acoli',
          'ada': 'Adangme',
          'ady': 'Adyghe',
          'aa': 'Afar',
          'afh': 'Afrihili',
          'af': 'Afrikaans',
          'agq': 'Aghem',
          'ain': 'Ainu',
          'ak': 'Akan',
          'akk': 'Akkadian',
          'bss': 'Akoose',
          'akz': 'Alabama',
          'sq': 'Albanian',
          'ale': 'Aleut',
          'arq': 'Algerian Arabic',
          'en_US': 'American English',
          'ase': 'American Sign Language',
          'am': 'Amharic',
          'egy': 'Ancient Egyptian',
          'grc': 'Ancient Greek',
          'anp': 'Angika',
          'njo': 'Ao Naga',
          'ar': 'Arabic',
          'an': 'Aragonese',
          'arc': 'Aramaic',
          'aro': 'Araona',
          'arp': 'Arapaho',
          'arw': 'Arawak',
          'hy': 'Armenian',
          'rup': 'Aromanian',
          'frp': 'Arpitan',
          'as': 'Assamese',
          'ast': 'Asturian',
          'asa': 'Asu',
          'cch': 'Atsam',
          'en_AU': 'Australian English',
          'de_AT': 'Austrian German',
          'av': 'Avaric',
          'ae': 'Avestan',
          'awa': 'Awadhi',
          'ay': 'Aymara',
          'az': 'Azerbaijani',
          'bfq': 'Badaga',
          'ksf': 'Bafia',
          'bfd': 'Bafut',
          'bqi': 'Bakhtiari',
          'ban': 'Balinese',
          'bal': 'Baluchi',
          'bm': 'Bambara',
          'bax': 'Bamun',
          'bjn': 'Banjar',
          'bas': 'Basaa',
          'ba': 'Bashkir',
          'eu': 'Basque',
          'bbc': 'Batak Toba',
          'bar': 'Bavarian',
          'bej': 'Beja',
          'be': 'Belarusian',
          'bem': 'Bemba',
          'bez': 'Bena',
          'bn': 'Bengali',
          'bew': 'Betawi',
          'bho': 'Bhojpuri',
          'bik': 'Bikol',
          'bin': 'Bini',
          'bpy': 'Bishnupriya',
          'bi': 'Bislama',
          'byn': 'Blin',
          'zbl': 'Blissymbols',
          'brx': 'Bodo',
          'bs': 'Bosnian',
          'brh': 'Brahui',
          'bra': 'Braj',
          'pt_BR': 'Brazilian Portuguese',
          'br': 'Breton',
          'en_GB': 'British English',
          'bug': 'Buginese',
          'bg': 'Bulgarian',
          'bum': 'Bulu',
          'bua': 'Buriat',
          'my': 'Burmese',
          'cad': 'Caddo',
          'frc': 'Cajun French',
          'en_CA': 'Canadian English',
          'fr_CA': 'Canadian French',
          'yue': 'Cantonese',
          'cps': 'Capiznon',
          'car': 'Carib',
          'ca': 'Catalan',
          'cay': 'Cayuga',
          'ceb': 'Cebuano',
          'tzm': 'Central Atlas Tamazight',
          'dtp': 'Central Dusun',
          'ckb': 'Central Kurdish',
          'esu': 'Central Yupik',
          'shu': 'Chadian Arabic',
          'chg': 'Chagatai',
          'ch': 'Chamorro',
          'ce': 'Chechen',
          'chr': 'Cherokee',
          'chy': 'Cheyenne',
          'chb': 'Chibcha',
          'cgg': 'Chiga',
          'qug': 'Chimborazo Highland Quichua',
          'zh': 'Chinese',
          'chn': 'Chinook Jargon',
          'chp': 'Chipewyan',
          'cho': 'Choctaw',
          'cu': 'Church Slavic',
          'chk': 'Chuukese',
          'cv': 'Chuvash',
          'nwc': 'Classical Newari',
          'syc': 'Classical Syriac',
          'ksh': 'Colognian',
          'swb': 'Comorian',
          'swc': 'Congo Swahili',
          'cop': 'Coptic',
          'kw': 'Cornish',
          'co': 'Corsican',
          'cr': 'Cree',
          'mus': 'Creek',
          'crh': 'Crimean Turkish',
          'hr': 'Croatian',
          'cs': 'Czech',
          'dak': 'Dakota',
          'da': 'Danish',
          'dar': 'Dargwa',
          'dzg': 'Dazaga',
          'del': 'Delaware',
          'din': 'Dinka',
          'dv': 'Divehi',
          'doi': 'Dogri',
          'dgr': 'Dogrib',
          'dua': 'Duala',
          'nl': 'Dutch',
          'dyu': 'Dyula',
          'dz': 'Dzongkha',
          'frs': 'Eastern Frisian',
          'efi': 'Efik',
          'arz': 'Egyptian Arabic',
          'eka': 'Ekajuk',
          'elx': 'Elamite',
          'ebu': 'Embu',
          'egl': 'Emilian',
          'en': 'English',
          'myv': 'Erzya',
          'eo': 'Esperanto',
          'et': 'Estonian',
          'pt_PT': 'European Portuguese',
          'es_ES': 'European Spanish',
          'ee': 'Ewe',
          'ewo': 'Ewondo',
          'ext': 'Extremaduran',
          'fan': 'Fang',
          'fat': 'Fanti',
          'fo': 'Faroese',
          'hif': 'Fiji Hindi',
          'fj': 'Fijian',
          'fil': 'Filipino',
          'fi': 'Finnish',
          'nl_BE': 'Flemish',
          'fon': 'Fon',
          'gur': 'Frafra',
          'fr': 'French',
          'fur': 'Friulian',
          'ff': 'Fulah',
          'gaa': 'Ga',
          'gag': 'Gagauz',
          'gl': 'Galician',
          'gan': 'Gan Chinese',
          'lg': 'Ganda',
          'gay': 'Gayo',
          'gba': 'Gbaya',
          'gez': 'Geez',
          'ka': 'Georgian',
          'de': 'German',
          'aln': 'Gheg Albanian',
          'bbj': 'Ghomala',
          'glk': 'Gilaki',
          'gil': 'Gilbertese',
          'gom': 'Goan Konkani',
          'gon': 'Gondi',
          'gor': 'Gorontalo',
          'got': 'Gothic',
          'grb': 'Grebo',
          'el': 'Greek',
          'gn': 'Guarani',
          'gu': 'Gujarati',
          'guz': 'Gusii',
          'gwi': 'Gwich\u02bcin',
          'hai': 'Haida',
          'ht': 'Haitian',
          'hak': 'Hakka Chinese',
          'ha': 'Hausa',
          'haw': 'Hawaiian',
          'he': 'Hebrew',
          'hz': 'Herero',
          'hil': 'Hiligaynon',
          'hi': 'Hindi',
          'ho': 'Hiri Motu',
          'hit': 'Hittite',
          'hmn': 'Hmong',
          'hu': 'Hungarian',
          'hup': 'Hupa',
          'iba': 'Iban',
          'ibb': 'Ibibio',
          'is': 'Icelandic',
          'io': 'Ido',
          'ig': 'Igbo',
          'ilo': 'Iloko',
          'smn': 'Inari Sami',
          'id': 'Indonesian',
          'izh': 'Ingrian',
          'inh': 'Ingush',
          'ia': 'Interlingua',
          'ie': 'Interlingue',
          'iu': 'Inuktitut',
          'ik': 'Inupiaq',
          'ga': 'Irish',
          'it': 'Italian',
          'jam': 'Jamaican Creole English',
          'ja': 'Japanese',
          'jv': 'Javanese',
          'kaj': 'Jju',
          'dyo': 'Jola-Fonyi',
          'jrb': 'Judeo-Arabic',
          'jpr': 'Judeo-Persian',
          'jut': 'Jutish',
          'kbd': 'Kabardian',
          'kea': 'Kabuverdianu',
          'kab': 'Kabyle',
          'kac': 'Kachin',
          'kgp': 'Kaingang',
          'kkj': 'Kako',
          'kl': 'Kalaallisut',
          'kln': 'Kalenjin',
          'xal': 'Kalmyk',
          'kam': 'Kamba',
          'kbl': 'Kanembu',
          'kn': 'Kannada',
          'kr': 'Kanuri',
          'kaa': 'Kara-Kalpak',
          'krc': 'Karachay-Balkar',
          'krl': 'Karelian',
          'ks': 'Kashmiri',
          'csb': 'Kashubian',
          'kaw': 'Kawi',
          'kk': 'Kazakh',
          'ken': 'Kenyang',
          'kha': 'Khasi',
          'km': 'Khmer',
          'kho': 'Khotanese',
          'khw': 'Khowar',
          'ki': 'Kikuyu',
          'kmb': 'Kimbundu',
          'krj': 'Kinaray-a',
          'rw': 'Kinyarwanda',
          'kiu': 'Kirmanjki',
          'tlh': 'Klingon',
          'bkm': 'Kom',
          'kv': 'Komi',
          'koi': 'Komi-Permyak',
          'kg': 'Kongo',
          'kok': 'Konkani',
          'ko': 'Korean',
          'kfo': 'Koro',
          'kos': 'Kosraean',
          'avk': 'Kotava',
          'khq': 'Koyra Chiini',
          'ses': 'Koyraboro Senni',
          'kpe': 'Kpelle',
          'kri': 'Krio',
          'kj': 'Kuanyama',
          'kum': 'Kumyk',
          'ku': 'Kurdish',
          'kru': 'Kurukh',
          'kut': 'Kutenai',
          'nmg': 'Kwasio',
          'ky': 'Kyrgyz',
          'quc': 'K\u02bciche\u02bc',
          'lad': 'Ladino',
          'lah': 'Lahnda',
          'lkt': 'Lakota',
          'lam': 'Lamba',
          'lag': 'Langi',
          'lo': 'Lao',
          'ltg': 'Latgalian',
          'la': 'Latin',
          'es_419': 'Latin American Spanish',
          'lv': 'Latvian',
          'lzz': 'Laz',
          'lez': 'Lezghian',
          'lij': 'Ligurian',
          'li': 'Limburgish',
          'ln': 'Lingala',
          'lfn': 'Lingua Franca Nova',
          'lzh': 'Literary Chinese',
          'lt': 'Lithuanian',
          'liv': 'Livonian',
          'jbo': 'Lojban',
          'lmo': 'Lombard',
          'nds': 'Low German',
          'sli': 'Lower Silesian',
          'dsb': 'Lower Sorbian',
          'loz': 'Lozi',
          'lu': 'Luba-Katanga',
          'lua': 'Luba-Lulua',
          'lui': 'Luiseno',
          'smj': 'Lule Sami',
          'lun': 'Lunda',
          'luo': 'Luo',
          'lb': 'Luxembourgish',
          'luy': 'Luyia',
          'mde': 'Maba',
          'mk': 'Macedonian',
          'jmc': 'Machame',
          'mad': 'Madurese',
          'maf': 'Mafa',
          'mag': 'Magahi',
          'vmf': 'Main-Franconian',
          'mai': 'Maithili',
          'mak': 'Makasar',
          'mgh': 'Makhuwa-Meetto',
          'kde': 'Makonde',
          'mg': 'Malagasy',
          'ms': 'Malay',
          'ml': 'Malayalam',
          'mt': 'Maltese',
          'mnc': 'Manchu',
          'mdr': 'Mandar',
          'man': 'Mandingo',
          'mni': 'Manipuri',
          'gv': 'Manx',
          'mi': 'Maori',
          'arn': 'Mapuche',
          'mr': 'Marathi',
          'chm': 'Mari',
          'mh': 'Marshallese',
          'mwr': 'Marwari',
          'mas': 'Masai',
          'mzn': 'Mazanderani',
          'byv': 'Medumba',
          'men': 'Mende',
          'mwv': 'Mentawai',
          'mer': 'Meru',
          'mgo': 'Meta\u02bc',
          'es_MX': 'Mexican Spanish',
          'mic': 'Micmac',
          'dum': 'Middle Dutch',
          'enm': 'Middle English',
          'frm': 'Middle French',
          'gmh': 'Middle High German',
          'mga': 'Middle Irish',
          'nan': 'Min Nan Chinese',
          'min': 'Minangkabau',
          'xmf': 'Mingrelian',
          'mwl': 'Mirandese',
          'lus': 'Mizo',
          'ar_001': 'Modern Standard Arabic',
          'moh': 'Mohawk',
          'mdf': 'Moksha',
          'ro_MD': 'Moldavian',
          'lol': 'Mongo',
          'mn': 'Mongolian',
          'mfe': 'Morisyen',
          'ary': 'Moroccan Arabic',
          'mos': 'Mossi',
          'mul': 'Multiple Languages',
          'mua': 'Mundang',
          'ttt': 'Muslim Tat',
          'mye': 'Myene',
          'naq': 'Nama',
          'na': 'Nauru',
          'nv': 'Navajo',
          'ng': 'Ndonga',
          'nap': 'Neapolitan',
          'ne': 'Nepali',
          'new': 'Newari',
          'sba': 'Ngambay',
          'nnh': 'Ngiemboon',
          'jgo': 'Ngomba',
          'yrl': 'Nheengatu',
          'nia': 'Nias',
          'niu': 'Niuean',
          'zxx': 'No linguistic content',
          'nog': 'Nogai',
          'nd': 'North Ndebele',
          'frr': 'Northern Frisian',
          'se': 'Northern Sami',
          'nso': 'Northern Sotho',
          'no': 'Norwegian',
          'nb': 'Norwegian Bokm\u00e5l',
          'nn': 'Norwegian Nynorsk',
          'nov': 'Novial',
          'nus': 'Nuer',
          'nym': 'Nyamwezi',
          'ny': 'Nyanja',
          'nyn': 'Nyankole',
          'tog': 'Nyasa Tonga',
          'nyo': 'Nyoro',
          'nzi': 'Nzima',
          'nqo': 'N\u02bcKo',
          'oc': 'Occitan',
          'oj': 'Ojibwa',
          'ang': 'Old English',
          'fro': 'Old French',
          'goh': 'Old High German',
          'sga': 'Old Irish',
          'non': 'Old Norse',
          'peo': 'Old Persian',
          'pro': 'Old Proven\u00e7al',
          'or': 'Oriya',
          'om': 'Oromo',
          'osa': 'Osage',
          'os': 'Ossetic',
          'ota': 'Ottoman Turkish',
          'pal': 'Pahlavi',
          'pfl': 'Palatine German',
          'pau': 'Palauan',
          'pi': 'Pali',
          'pam': 'Pampanga',
          'pag': 'Pangasinan',
          'pap': 'Papiamento',
          'ps': 'Pashto',
          'pdc': 'Pennsylvania German',
          'fa': 'Persian',
          'phn': 'Phoenician',
          'pcd': 'Picard',
          'pms': 'Piedmontese',
          'pdt': 'Plautdietsch',
          'pon': 'Pohnpeian',
          'pl': 'Polish',
          'pnt': 'Pontic',
          'pt': 'Portuguese',
          'prg': 'Prussian',
          'pa': 'Punjabi',
          'qu': 'Quechua',
          'raj': 'Rajasthani',
          'rap': 'Rapanui',
          'rar': 'Rarotongan',
          'rif': 'Riffian',
          'rgn': 'Romagnol',
          'ro': 'Romanian',
          'rm': 'Romansh',
          'rom': 'Romany',
          'rof': 'Rombo',
          'root': 'Root',
          'rtm': 'Rotuman',
          'rug': 'Roviana',
          'rn': 'Rundi',
          'ru': 'Russian',
          'rue': 'Rusyn',
          'rwk': 'Rwa',
          'ssy': 'Saho',
          'sah': 'Sakha',
          'sam': 'Samaritan Aramaic',
          'saq': 'Samburu',
          'sm': 'Samoan',
          'sgs': 'Samogitian',
          'sad': 'Sandawe',
          'sg': 'Sango',
          'sbp': 'Sangu',
          'sa': 'Sanskrit',
          'sat': 'Santali',
          'sc': 'Sardinian',
          'sas': 'Sasak',
          'sdc': 'Sassarese Sardinian',
          'stq': 'Saterland Frisian',
          'saz': 'Saurashtra',
          'sco': 'Scots',
          'gd': 'Scottish Gaelic',
          'sly': 'Selayar',
          'sel': 'Selkup',
          'seh': 'Sena',
          'see': 'Seneca',
          'sr': 'Serbian',
          'sh': 'Serbo-Croatian',
          'srr': 'Serer',
          'sei': 'Seri',
          'ksb': 'Shambala',
          'shn': 'Shan',
          'sn': 'Shona',
          'ii': 'Sichuan Yi',
          'scn': 'Sicilian',
          'sid': 'Sidamo',
          'bla': 'Siksika',
          'szl': 'Silesian',
          'zh_Hans': 'Simplified Chinese',
          'sd': 'Sindhi',
          'si': 'Sinhala',
          'sms': 'Skolt Sami',
          'den': 'Slave',
          'sk': 'Slovak',
          'sl': 'Slovenian',
          'xog': 'Soga',
          'sog': 'Sogdien',
          'so': 'Somali',
          'snk': 'Soninke',
          'azb': 'South Azerbaijani',
          'nr': 'South Ndebele',
          'alt': 'Southern Altai',
          'sma': 'Southern Sami',
          'st': 'Southern Sotho',
          'es': 'Spanish',
          'srn': 'Sranan Tongo',
          'zgh': 'Standard Moroccan Tamazight',
          'suk': 'Sukuma',
          'sux': 'Sumerian',
          'su': 'Sundanese',
          'sus': 'Susu',
          'sw': 'Swahili',
          'ss': 'Swati',
          'sv': 'Swedish',
          'fr_CH': 'Swiss French',
          'gsw': 'Swiss German',
          'de_CH': 'Swiss High German',
          'syr': 'Syriac',
          'shi': 'Tachelhit',
          'tl': 'Tagalog',
          'ty': 'Tahitian',
          'dav': 'Taita',
          'tg': 'Tajik',
          'tly': 'Talysh',
          'tmh': 'Tamashek',
          'ta': 'Tamil',
          'trv': 'Taroko',
          'twq': 'Tasawaq',
          'tt': 'Tatar',
          'te': 'Telugu',
          'ter': 'Tereno',
          'teo': 'Teso',
          'tet': 'Tetum',
          'th': 'Thai',
          'bo': 'Tibetan',
          'tig': 'Tigre',
          'ti': 'Tigrinya',
          'tem': 'Timne',
          'tiv': 'Tiv',
          'tli': 'Tlingit',
          'tpi': 'Tok Pisin',
          'tkl': 'Tokelau',
          'to': 'Tongan',
          'fit': 'Tornedalen Finnish',
          'zh_Hant': 'Traditional Chinese',
          'tkr': 'Tsakhur',
          'tsd': 'Tsakonian',
          'tsi': 'Tsimshian',
          'ts': 'Tsonga',
          'tn': 'Tswana',
          'tcy': 'Tulu',
          'tum': 'Tumbuka',
          'aeb': 'Tunisian Arabic',
          'tr': 'Turkish',
          'tk': 'Turkmen',
          'tru': 'Turoyo',
          'tvl': 'Tuvalu',
          'tyv': 'Tuvinian',
          'tw': 'Twi',
          'kcg': 'Tyap',
          'udm': 'Udmurt',
          'uga': 'Ugaritic',
          'uk': 'Ukrainian',
          'umb': 'Umbundu',
          'und': 'Unknown Language',
          'hsb': 'Upper Sorbian',
          'ur': 'Urdu',
          'ug': 'Uyghur',
          'uz': 'Uzbek',
          'vai': 'Vai',
          've': 'Venda',
          'vec': 'Venetian',
          'vep': 'Veps',
          'vi': 'Vietnamese',
          'vo': 'Volap\u00fck',
          'vro': 'V\u00f5ro',
          'vot': 'Votic',
          'vun': 'Vunjo',
          'wa': 'Walloon',
          'wae': 'Walser',
          'war': 'Waray',
          'wbp': 'Warlpiri',
          'was': 'Washo',
          'guc': 'Wayuu',
          'cy': 'Welsh',
          'vls': 'West Flemish',
          'fy': 'Western Frisian',
          'mrj': 'Western Mari',
          'wal': 'Wolaytta',
          'wo': 'Wolof',
          'wuu': 'Wu Chinese',
          'xh': 'Xhosa',
          'hsn': 'Xiang Chinese',
          'yav': 'Yangben',
          'yao': 'Yao',
          'yap': 'Yapese',
          'ybb': 'Yemba',
          'yi': 'Yiddish',
          'yo': 'Yoruba',
          'zap': 'Zapotec',
          'dje': 'Zarma',
          'zza': 'Zaza',
          'zea': 'Zeelandic',
          'zen': 'Zenaga',
          'za': 'Zhuang',
          'gbz': 'Zoroastrian Dari',
          'zu': 'Zulu',
          'zun': 'Zuni'
        };
        return languages[language] || languages[language.split('-')[0]];
      }
    }
  }
});

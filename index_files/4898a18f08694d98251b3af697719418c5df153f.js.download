'use strict';

$$.jqueryPlugin('alerts', {
  defaultOptions: {
    label: 'alert',
    lsName: 'theme:alert'
  },
  methods: {
    init: function init(uid, options, $container) {
      var _this = this;

      this.options = options;
      this.$container = $container;
      this.alerts = {};
      new $$.Request().fetch('/api/v2/help_center/articles.json', {
        label_names: this.options.label,
        draft: false,
        locale: window.locale
      }).then(function (data) {
        _this.handleRequestSuccess(data);

        _this.bindEvents();
      })["catch"](this.handleRequestError.bind(this));
    },
    bindEvents: function bindEvents() {
      var click = 'click.' + this.uid + ' enterkey.' + this.uid;
      $(window).on('resize', this.setHeightToContainer.bind(this));
      this.$container.on(click, '.js-alert-close', this.handleAlertClose.bind(this));
    },
    handleRequestSuccess: function handleRequestSuccess(data) {
      var _this2 = this;

      data.articles.forEach(function (article) {
        if (!localStorage.getItem("".concat(_this2.options.lsName, ":").concat(article.id))) {
          _this2.createAlert(article);
        }
      });
      Object.keys(this.alerts).forEach(function (position) {
        var $alerts = $("\n            <div class=\"alerts alerts--".concat(position, "\">\n                ").concat(_this2.alerts[position].join(''), "\n            </div>\n        "));

        _this2.$container.append($alerts[0]);
      });
      this.setHeightToContainer();
    },
    handleRequestError: function handleRequestError() {
      this.$container.addClass('is-error');
    },
    handleAlertClose: function handleAlertClose(event) {
      event.preventDefault();
      var $link = $(event.currentTarget);
      var $alert = $link.closest('.alert');
      var articleId = $(event.currentTarget).data('article-id');
      localStorage.setItem("".concat(this.options.lsName, ":").concat(articleId), 'true');
      $alert.remove();
      this.setHeightToContainer();
    },
    setHeightToContainer: function setHeightToContainer() {
      this.$container.height(this.$container.children('.alerts--default').height());
    },
    createAlert: function createAlert(article) {
      var style = 'default';
      var position = 'default';
      var ALERT_STYLE = "".concat(this.options.label, "-(info|success|warning|danger)");
      var ALERT_POSITION = "".concat(this.options.label, "-(tl|tc|tr|bl|bc|br)");
      var IS_ALERT_LINK = article.label_names.indexOf("".concat(this.options.label, "-link")) !== -1;
      var IS_ALERT_TITLE = article.label_names.indexOf("".concat(this.options.label, "-title")) !== -1;
      var IS_ALERT_BODY = article.label_names.indexOf("".concat(this.options.label, "-body")) !== -1;
      var labelsStr = article.label_names.join();
      labelsStr.replace(new RegExp(ALERT_STYLE, 'i'), function (m, alertStyle) {
        style = alertStyle;
      });
      labelsStr.replace(new RegExp(ALERT_POSITION, 'i'), function (m, alertPos) {
        position = alertPos;
      });

      if (!this.alerts[position]) {
        this.alerts[position] = [];
      }

      var html = "\n          <div class=\"alert alert--".concat(style, "\">\n              <div class=\"container alert__container\">\n                  <div class=\"alert__box\">\n      ");

      if (IS_ALERT_LINK) {
        html += "<a href=\"".concat(article.html_url, "\" class=\"alert__link\"></a>");
      }

      if (IS_ALERT_TITLE) {
        html += "<div class=\"alert__title\">".concat(article.title, "</div>");
      } else if (IS_ALERT_BODY) {
        html += "<div class=\"alert__body\">".concat(article.body, "</div>");
      } else {
        html += "\n            <div class=\"alert__title\">".concat(article.title, "</div>\n            <div class=\"alert__body\">").concat(article.body, "</div>\n        ");
      }

      html += '</div>';
      html += "<a class=\"btn btn--circle btn--sm alert__close js-alert-close\" data-article-id=\"".concat(article.id, "\" tabindex=\"0\" role=\"button\" aria-label=\"Close\">\n                   <i class=\"fas fa-times\"></i>\n               </a>");
      html += '</div></div>';
      this.alerts[position].push(html);
    }
  }
});
$(function () {
  if (!$('[data-plugin-alerts]').length) {
    var $alerts = $('<div></div>');
    var $preview = $('#preview-bar-container');

    if ($preview.length) {
      $preview.after($alerts[0]);
    } else {
      $('body').prepend($alerts[0]);
    }

    $alerts.alerts();
  }
});

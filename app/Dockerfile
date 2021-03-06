FROM redmine:3.2.5-passenger

LABEL maintainer "Hidetoshi Imai <hidepin@gmail.com>"

ENV REDMINE_ISSUE_TEMPLATES_VERSION=0.1.2 \
    CLIPBOARD_IMAGE_PASTE_VERSION=1.10 \
    REDMINE_CODE_REVIEW_VERSION=0.7.0 \
    REDMINE_BANNER_VERSION=0.1.2

# パッケージの追加インストール
# - fonts-ipafont-gothic
RUN buildDeps=' \
                unzip \
                fonts-ipafont-gothic \
    ' \
    && set -x \
    && apt-get update && apt-get install -y --no-install-recommends $buildDeps && rm -rf /var/lib/apt/lists/*

# 設定の追加
# - 日本語フォント設定
RUN sed 's#rmagick_font_path:$#rmagick_font_path: /usr/share/fonts/opentype/ipafont-gothic/ipag.ttf#' config/configuration.yml.example > config/configuration.yml

# プラグインとテーマを追加
# - プラグイン
#   - redmine_issue_templates
#   - clipboard_image_paste
#   - redmine_code_review
#   - redmine_github_hook
#   - sidebar_hide
#   - redmine_banner
#   - redmine_incr_code_search
#   - redmine_emoji
#   - redmine_mattermost
#   - redmine-view-customize
# - テーマ
#   - farend_basic
RUN cd plugins && \
    wget -O ${REDMINE_ISSUE_TEMPLATES_VERSION}.tar.gz https://github.com/akiko-pusu/redmine_issue_templates/archive/${REDMINE_ISSUE_TEMPLATES_VERSION}.tar.gz && \
    tar xvf ${REDMINE_ISSUE_TEMPLATES_VERSION}.tar.gz && \
    rm ${REDMINE_ISSUE_TEMPLATES_VERSION}.tar.gz && \
    mv redmine_issue_templates-${REDMINE_ISSUE_TEMPLATES_VERSION} redmine_issue_templates && \
    chown -R redmine:redmine redmine_issue_templates && \
    wget -O v${CLIPBOARD_IMAGE_PASTE_VERSION}.tar.gz https://github.com/peclik/clipboard_image_paste/archive/v${CLIPBOARD_IMAGE_PASTE_VERSION}.tar.gz && \
    tar xvf v${CLIPBOARD_IMAGE_PASTE_VERSION}.tar.gz && \
    rm v${CLIPBOARD_IMAGE_PASTE_VERSION}.tar.gz && \
    mv clipboard_image_paste-${CLIPBOARD_IMAGE_PASTE_VERSION} clipboard_image_paste && \
    chown -R redmine:redmine clipboard_image_paste && \
    git clone --depth 1 https://github.com/koppen/redmine_github_hook.git && \
    chown -R redmine:redmine redmine_github_hook && \
    wget -O redmine_code_review-${REDMINE_CODE_REVIEW_VERSION}.zip https://bitbucket.org/haru_iida/redmine_code_review/downloads/redmine_code_review-${REDMINE_CODE_REVIEW_VERSION}.zip && \
    unzip redmine_code_review-${REDMINE_CODE_REVIEW_VERSION}.zip && \
    rm redmine_code_review-${REDMINE_CODE_REVIEW_VERSION}.zip && \
    chown -R redmine:redmine redmine_code_review && \
    git clone --depth 1 https://github.com/bdemirkir/sidebar_hide.git && \
    chown -R redmine:redmine sidebar_hide && \
    wget -O ${REDMINE_BANNER_VERSION}.tar.gz https://github.com/akiko-pusu/redmine_banner/archive/${REDMINE_BANNER_VERSION}.tar.gz && \
    tar xvf ${REDMINE_BANNER_VERSION}.tar.gz && \
    mv redmine_banner-${REDMINE_BANNER_VERSION} redmine_banner && \
    rm ${REDMINE_BANNER_VERSION}.tar.gz && \
    chown -R redmine:redmine redmine_banner && \
    git clone --depth 1 https://github.com/suer/redmine_incr_code_search.git && \
    chown -R redmine:redmine redmine_incr_code_search && \
    git clone --depth 1 https://github.com/suer/redmine_emoji.git && \
    chown -R redmine:redmine redmine_emoji && \
    git clone --depth 1 https://github.com/altsol/redmine_mattermost.git && \
    sed -i.org 's/\(.*client.ssl_config.ssl_version = "SSLv23"\)/\1\n\t\t\tclient.ssl_config.verify_mode = OpenSSL::SSL::VERIFY_NONE/' /usr/src/redmine/plugins/redmine_mattermost/lib/redmine_mattermost/listener.rb && \
    chown -R redmine:redmine  redmine_mattermost && \
    git clone --depth 1 https://github.com/onozaty/redmine-view-customize.git view_customize && \
    chown -R redmine:redmine view_customize && \
    sed -i.org1 's/gosu redmine rake db:migrate/gosu redmine rake db:migrate redmine:plugins:migrate RAILS_ENV=production/' /docker-entrypoint.sh && \
    sed -i.org2 's#\(bundle install.*$\)#if [ -z "$REDMINE_NO_DB_MIGRATE" ]; then \1 ;fi#' /docker-entrypoint.sh && \
    cd ../public/themes && \
    git clone --depth 1 https://github.com/farend/redmine_theme_farend_basic.git farend_basic && \
    chown -R redmine:redmine farend_basic

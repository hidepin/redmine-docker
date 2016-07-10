FROM redmine:3.2.3-passenger

ENV REDMINE_ISSUE_TEMPLATES_VERSION=0.1.2 \
    CLIPBOARD_IMAGE_PASTE_VERSION=1.10

# パッケージの追加インストール
# - fonts-ipafont-gothic
RUN buildDeps=' \
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
# - テーマ
#   - farend_basic
RUN cd plugins && \
    curl -L -O https://github.com/akiko-pusu/redmine_issue_templates/archive/${REDMINE_ISSUE_TEMPLATES_VERSION}.tar.gz && \
    tar xvf ${REDMINE_ISSUE_TEMPLATES_VERSION}.tar.gz && \
    rm ${REDMINE_ISSUE_TEMPLATES_VERSION}.tar.gz && \
    mv redmine_issue_templates-${REDMINE_ISSUE_TEMPLATES_VERSION} redmine_issue_templates && \
    chown -R redmine:redmine redmine_issue_templates && \
    curl -L -O https://github.com/peclik/clipboard_image_paste/archive/v${CLIPBOARD_IMAGE_PASTE_VERSION}.tar.gz && \
    tar xvf v${CLIPBOARD_IMAGE_PASTE_VERSION}.tar.gz && \
    rm v${CLIPBOARD_IMAGE_PASTE_VERSION}.tar.gz && \
    mv clipboard_image_paste-${CLIPBOARD_IMAGE_PASTE_VERSION} clipboard_image_paste && \
    chown -R redmine:redmine clipboard_image_paste && \
    sed -i.org 's/gosu redmine rake db:migrate/gosu redmine rake db:migrate redmine:plugins:migrate RAILS_ENV=production/' /docker-entrypoint.sh && \
    cd ../public/themes && \
    git clone --depth 1 https://github.com/farend/redmine_theme_farend_basic.git farend_basic && \
    chown -R redmine:redmine farend_basic

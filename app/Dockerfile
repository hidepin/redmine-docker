FROM redmine:3.2.3-passenger

ENV REDMINE_ISSUE_TEMPLATES_VERSION 0.1.2

RUN cd plugins && \
    curl -L -O https://github.com/akiko-pusu/redmine_issue_templates/archive/${REDMINE_ISSUE_TEMPLATES_VERSION}.tar.gz && \
    tar xvf ${REDMINE_ISSUE_TEMPLATES_VERSION}.tar.gz && \
    rm ${REDMINE_ISSUE_TEMPLATES_VERSION}.tar.gz && \
    mv redmine_issue_templates-${REDMINE_ISSUE_TEMPLATES_VERSION} redmine_issue_templates && \
    sed -i.org 's/gosu redmine rake db:migrate/gosu redmine rake db:migrate redmine:plugins:migrate RAILS_ENV=production/' /docker-entrypoint.sh

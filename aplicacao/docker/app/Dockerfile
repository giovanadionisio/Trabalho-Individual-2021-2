FROM ruby:3.0.0
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -
RUN apt-get update -qq && apt-get install -qq --no-install-recommends \
    nodejs \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
RUN npm install -g yarn@1
WORKDIR /aplicacao
COPY Gemfile /aplicacao/Gemfile
COPY Gemfile.lock /aplicacao/Gemfile.lock
RUN bundle install
COPY . /aplicacao
EXPOSE 3000
RUN SECRET_KEY_BASE=1 RAILS_ENV=development bundle exec rake assets:precompile
CMD ["bundle", "exec", "puma", "-C", "config/puma.rb"] 
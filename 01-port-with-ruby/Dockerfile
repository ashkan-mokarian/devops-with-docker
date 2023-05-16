# Dockerfile for running example in here: https://devopswithdocker.com/part-1/section-6

# From the README file we can write the following dockerfile to containerize this repo: https://github.com/docker-hy/material-applications/tree/main/rails-example-project

# need ruby 3.1.0
FROM ruby:3.1.0

EXPOSE 3000

WORKDIR /usr/src/app

# still following from the repo README
RUN gem install bundler:2.3.3

# I don't get this part, but we have to copy the gem files
COPY rails-example-project/Gemfile* ./

RUN bundle install

COPY rails-example-project/. .

# running for production mode
RUN rails db:migrate RAILS_ENV=production

RUN rake assets:precompile

# These were all from the readme of the repo we wanted to containerize, now run the command
CMD ["rails", "s", "-e", "production"]
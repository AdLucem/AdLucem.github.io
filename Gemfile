source "https://rubygems.org"

# GitHub Pages builds this site with the github-pages gem -- Jekyll 3.10 and
# Ruby Sass 3.7, not a Jekyll of our choosing -- because Pages deploys from a
# branch. Depending on the same gem keeps `bundle exec jekyll build` here
# identical to what actually deploys, instead of passing locally on Jekyll 4
# and failing in CI on syntax the older Sass cannot parse.
gem "github-pages", group: :jekyll_plugins

# Jekyll 3.10 on Ruby 3.x needs an explicit webrick for `jekyll serve`.
gem "webrick", "~> 1.7"

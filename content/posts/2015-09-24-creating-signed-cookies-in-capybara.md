---
title: "Creating Signed Cookies in Capybara"
date: 2015-09-24
draft: false
toc: false
images:
tags:
  - programming
  - ruby-on-rails
---

I struggled with this for a while, but I finally got it.

## Backstory

I have a series of Rails applications that authenticates through a single OAuth provider app. After authenticating successfully, the OAuth provider creates a multi-domain signed cookie that can be read by all of the apps, so the user will only have to authenticate once. It’s sort of a Single Sign On (SSO) solution that our team thought of a year ago.

Unfortunately, we didn’t have enough knowledge and time to create automated tests, so we just made the feature without writing the tests. Fast forward to today, I decided to go back and do the right thing, so here we are.

## The Problem

At the time of writing, I haven’t found a solution on how to create signed cookies in Capybara on the Internet. The closest thing I got was this post, but it wasn’t exactly the solution that I was looking for. It was also outdated and it wasn’t working for Rails 4.1.

## The solution

```ruby
# spec/support/signed_cookie_generator.rb

class SignedCookieGenerator
  attr_accessor :name, :value

  def initialize(name, value)
    @name = name.to_sym
    @value = value
    @cookie_jar = ActionDispatch::Cookies::CookieJar.new(ActiveSupport::KeyGenerator.new(Rails.application.secrets.secret_key_base), Capybara.default_host, false, signed_cookie_salt: Rails.configuration.action_dispatch.encrypted_signed_cookie_salt)
  end

  def to_s
    "#{name}=#{signed_cookie_value};"
  end

  def signed_cookie_value
    @cookie_jar.signed[name] = {value: value}
    Rack::Utils.escape(@cookie_jar[name])
  end
end
```

I had to dig deep into the source of ActionDispatch just to understand what I was missing in the CookieJar creation. Here’s an example on how to use this little helper:

```ruby
# spec/features/profile_management_spec.rb

require 'rails_helper'

feature 'Profile Management', type: :feature do
  given!(:user) { create(:user) }

  scenario 'User updates profile' do
    cookie = SignedCookieGenerator.new(:auth, user.id)
    page.driver.browser.set_cookie(cookie.to_s)
    visit '/account/profile'

    expect(page).to have_text('Profile Page')
  end
end
```

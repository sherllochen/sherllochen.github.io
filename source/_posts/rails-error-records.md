---
title: Rails error records
date: 2020-02-13 05:16:25
keywords: rails error
description:
tags:
  - rails
---
> Records for rails errors and solutions.

**Error:** Deploy processing fail and expose error below, but no detail log for where the error occur.

**Error message:** Uglifier::Error: Unexpected token: punc (,)

**Solution:** Go into 'rails console' mode, and run script below. Code position where error occur will show up.

    JS_PATH = "app/assets/javascripts/**/*.js"; 
    Dir[JS_PATH].each do |file_name|
      puts "\n#{file_name}"
      puts Uglifier.compile(File.read(file_name), harmony: true)
    end

---
<!-- more -->
---

**Error: Precompile assets when deploying to production.**

**Error message:** Uglifier::Error: Unexpected token: punc ({). To use ES6 syntax, harmony mode must be enabled with Uglifier.new(:harmony => true).

**Reason:** ES6 syntax is use in normal js file.

**Solution:**

    # environments/production.rb
    # comment out this line
    # config.assets.js_compressor = :uglifier
    # add line below, enable harmony mode，allow to use ES6 syntax in normal js file
    config.assets.js_compressor = Uglifier.new(harmony: true)

---

---

**Error message:** Capybara::NotSupportedByDriverError

**Reason:** This error is raise by RackTest, because some feature tests have not set "js:true".

---

---

**Error:** Error message below show in some page.

**Error message:** An unhandled lowlevel error occurred. The application logs may have details.

**Reason:** Connection pool of mysql is used out.

**Solution:** The reason can be found in puma log. Path of puma log is config in config/puma.rb, and the default path is app_path/shared/log/puma.stderr.log. After review log in puma.stderr.log, some message show up: "could not obtain a database connection within 5.000 seconds (waited 5.000 seconds) (ActiveRecord::ConnectionTimeoutError)". Now we know what happened. Increase pool number in "config/database.yml" will solve it.

---

---

**Error: When execute "rails console".**

**Error message:** FATAL: Listen error: unable to monitor directories for changes.

**Reason:** x

**Solution:** x

    **echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p**

---

---

**Error:** When using kaminari

**Error message:**  undefined method "page"

**Reason:**  Kaminari has been installed in engine that project include. But the main can not use it.

**Solution:** Install kaminari gem in main project.

---

---

**Error:** active_model_serializers return data with original structure, but not defined in xx_serializers.rb.

**Reason:** xSome thing wrong in connected attribute, such as has_one :xxx definded in xx_serializers.rb, but actually there is not has_one relation, or the relation is has_many.

---

---

**Error:** Execute "rails console", and stuck for a long time, can't go into console mode.

**Reason:** Stuck by spring.

**Solution:**  Kill spring thread, and execute rails console again

---

---

**Error:** 执行joins查询时报错：ActiveRecord::ConfigurationError: Can't join 'Order' to association named 'sub_order'

**Error message:** x

**Reason:** x

**Solution:** xOrder.joins(:sub_order) 应该为 Order.joins(:sub_orders)才对，因为order has many sub_orders

---

---

**Error:** xProduction environment, there is Vue instance in the page, but after loaded, element of Vue disappeared.

**Solution:** In production environment, import command must like this.

    import Vue from 'vue/dist/vue.esm'

---

---

**Error:** Execute "cap production depoly".

**Error message:** cap aborted! Don't know how to build task 'depoly' (see --tasks)

**Solution:** Must be deploy, not deploy.

---

---

**Error: API request get 200 response, but no content.**

**Reason:** Some exception occured in controller or model, but not be raised, or not rescued. Like xx_model.save. When it failed, nothing exception will be raised. If controller not check the save result and response directly, it will cause this issue.

---

---

**Error message:** ActionDispatch::Cookies::CookieOverflow

**Reason:** It always occur in dev environment. Some Frontend Dependent file are not existed, but webpack-server was not restarted.  

**Solution:** Check the exist of these files, **r**estart webpack-server.

---

---

**Error message:** Capfile locked at 3.1.0, but 3.2.1 is loaded

**Solution:** Uninstall newer version of capistrano

    bundle uninstall capistrano

---

---

**Error message:** Webpacker can't find xx.js in manifest.json

**Solution:** Delete public/packs/manifest.json, restart ./bin/webpack-web-serser

---

---

**Error:** xAJAX API request was redirect to login page. There is "Can't verify CSRF token authenticity" in request log.

**Reason:** The request is block by protect_from_forgery.

**Solution:** api_request? must be defined to match all api request

    # application_controller.rb
    protect_from_forgery unless: -> {api_request?} 

---

---

**Error:** xUsing add_timestamps to add timestamps(created_at, updated_at) to existed tables will get "not default value" error. 

**Reason:** xcolumns add by add_timestamps are not allowed nil value.

**Solution:** xExecute add_timestamps with "null: true" to allow related columns to be nil, and then replenish these values. At last, set columns to "null: false"

    add_timestamps :one_models, null: true
    
    long_ago = DateTime.new(2000, 1, 1)
    
    OneModel.update_all(created_at: long_ago, updated_at: long_ago)
    
    change_column_null :one_models, :created_at, false
    
    change_column_null :one_models, :updated_at, false

---
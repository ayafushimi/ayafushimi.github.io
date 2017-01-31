---
layout: post
title:  "Ruby Documents I frequently reffer to"
date:   2016-12-16 20:22:50 -0500
---


## Ruby Documents (Ruby 2.3.1)

Classes

* [BasicObject](https://ruby-doc.org/core-2.3.1/BasicObject.html)
  * [Object](https://ruby-doc.org/core-2.3.1/Object.html)
    * [Array](https://ruby-doc.org/core-2.3.1/Array.html)
      * [compact](https://ruby-doc.org/core-2.3.1/Array.html#method-i-compact)
      * [concat](https://ruby-doc.org/core-2.3.1/Array.html#method-i-concat)
      * [empty?](https://ruby-doc.org/core-2.3.1/Array.html#method-i-empty-3F)
      * [flatten](https://ruby-doc.org/core-2.3.1/Array.html#method-i-flatten)
      * [include?](https://ruby-doc.org/core-2.3.1/Array.html#method-i-include-3F)
      * [join](https://ruby-doc.org/core-2.3.1/Array.html#method-i-join)
      * [select](https://ruby-doc.org/core-2.3.1/Array.html#method-i-select)
      * [uniq](https://ruby-doc.org/core-2.3.1/Array.html#method-i-uniq)
    * [Binding](https://ruby-doc.org/core-2.3.1/Binding.html)
      * [eval](https://ruby-doc.org/core-2.3.1/Binding.html#method-i-eval)
    * [Class](https://ruby-doc.org/core-2.3.1/Class.html)
      * [superclass](https://ruby-doc.org/core-2.3.1/Class.html#method-i-superclass)
    * [Dir](https://ruby-doc.org/core-2.3.1/Dir.html)
    * [ENV](https://ruby-doc.org/core-2.3.1/ENV.html)
    * [FalseClass](https://ruby-doc.org/core-2.3.1/FalseClass.html)
    * [Hash](https://ruby-doc.org/core-2.3.1/Hash.html)
    * [IO](https://ruby-doc.org/core-2.3.1/IO.html)
      * [File](https://ruby-doc.org/core-2.3.1/File.html)
    * [Method](https://ruby-doc.org/core-2.3.1/Method.html)
    * [Module](https://ruby-doc.org/core-2.3.1/Module.html)
      * [Class](https://ruby-doc.org/core-2.3.1/Class.html)
    * [NilClass](https://ruby-doc.org/core-2.3.1/NilClass.html)
    * [Numeric](https://ruby-doc.org/core-2.3.1/Numeric.html)
      * [Integer](https://ruby-doc.org/core-2.3.1/Integer.html)
        * [Fixnum](https://ruby-doc.org/core-2.3.1/Fixnum.html)
        * [Float](https://ruby-doc.org/core-2.3.1/Float.html)
        * [Rational](https://ruby-doc.org/core-2.3.1/Rational.html)
    * [Proc](https://ruby-doc.org/core-2.3.1/Proc.html)
    * [Random](https://ruby-doc.org/core-2.3.1/Random.html)
    * [Range](https://ruby-doc.org/core-2.3.1/Range.html)
    * [Regexp](https://ruby-doc.org/core-2.3.1/Regexp.html)
    * [String](https://ruby-doc.org/core-2.3.1/String.html)
    * [Symbol](https://ruby-doc.org/core-2.3.1/Symbol.html) 
    * [Time](https://ruby-doc.org/core-2.3.1/Time.html)
    * [TrueClass](https://ruby-doc.org/core-2.3.1/TrueClass.html)

Modules

* [Comparable](https://ruby-doc.org/core-2.3.1/Comparable.html)
*  [Enumerable](https://ruby-doc.org/core-2.3.1/Enumerable.html)
*  [Kernel](https://ruby-doc.org/core-2.3.1/Kernel.html)
*  [Math](https://ruby-doc.org/core-2.3.1/Math.html)


## Rails Documents


### ActiveRecord [(DOC)](http://api.rubyonrails.org/classes/ActiveRecord.html)

Active Record Basics [(Rails Guide)](http://guides.rubyonrails.org/active_record_basics.html)

* [FinderMethods](http://api.rubyonrails.org/classes/ActiveRecord/FinderMethods.html)
  * [find](http://api.rubyonrails.org/classes/ActiveRecord/FinderMethods.html#method-i-find)
  * [find_by](http://api.rubyonrails.org/classes/ActiveRecord/FinderMethods.html#method-i-find_by)
* [Persistence](http://api.rubyonrails.org/classes/ActiveRecord/Persistence.html)
  * [save](http://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-save)
  * [update](http://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-update)
  * [destroy](http://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-destroy)
  * [ClassMethods](http://api.rubyonrails.org/classes/ActiveRecord/Persistence/ClassMethods.html)
    * [instantiate](http://api.rubyonrails.org/classes/ActiveRecord/Persistence/ClassMethods.html#method-i-instantiate)
    * [create](http://api.rubyonrails.org/classes/ActiveRecord/Persistence/ClassMethods.html#method-i-create)
* [Relation](http://api.rubyonrails.org/classes/ActiveRecord/Relation.html)
  * [find_or_create_by](http://api.rubyonrails.org/classes/ActiveRecord/Relation.html#method-i-find_or_create_by)
  * [find_or_initialize_by](http://api.rubyonrails.org/classes/ActiveRecord/Relation.html#method-i-find_or_initialize_by)


Active Record Migrations [(Rails Guide)](http://guides.rubyonrails.org/active_record_migrations.html)

* [Migration](http://api.rubyonrails.org/classes/ActiveRecord/Migration.html)
* [ConnectionAdapters](http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters.html)
  * [SchemaStatements](http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/SchemaStatements.html)
    * [create_table](http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/SchemaStatements.html#method-i-create_table)
    * [add_column](http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/SchemaStatements.html#method-i-add_column)
    * [create_join_table](http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/SchemaStatements.html#method-i-create_join_table)

  * [Table](http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/Table.html)
    * [belongs_to](http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/Table.html#method-i-belongs_to)
    * [timestamps](http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/Table.html#method-i-timestamps)
  

Active Record Validations [(Rails Guide)](http://guides.rubyonrails.org/active_record_validations.html)

* [Validations](http://api.rubyonrails.org/classes/ActiveRecord/Validations.html)


Active Record Callbacks [(Rails Guide)](http://guides.rubyonrails.org/active_record_callbacks.html)

* [Callbacks](http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html)


Active Record Associations [(Rails Guide)](http://guides.rubyonrails.org/association_basics.html)

* [Associations](http://api.rubyonrails.org/classes/ActiveRecord/Associations.html)
  * [ClassMethods](http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html)
    * [belongs_to](http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html#method-i-belongs_to)
    * [has_many](http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html#method-i-has_many)
  * [CollectionProxy](http://api.rubyonrails.org/classes/ActiveRecord/Associations/CollectionProxy.html)


Active Record Query Interface [(Rails Guide)](http://guides.rubyonrails.org/active_record_querying.html)

* [QueryMethods](http://api.rubyonrails.org/classes/ActiveRecord/QueryMethods.html)
* [SpawnMethods](http://api.rubyonrails.org/classes/ActiveRecord/SpawnMethods.html)


Modules

* [Enum](http://api.rubyonrails.org/classes/ActiveRecord/Enum.html)
* [NestedAttributes](http://api.rubyonrails.org/classes/ActiveRecord/NestedAttributes.html)
  * [ClassMethods](http://api.rubyonrails.org/classes/ActiveRecord/NestedAttributes/ClassMethods.html)
    * [accepts_nested_attributes_for](http://api.rubyonrails.org/classes/ActiveRecord/NestedAttributes/ClassMethods.html#method-i-accepts_nested_attributes_for)



### ActiveModel [(DOC)](http://api.rubyonrails.org/classes/ActiveModel.html)

Active Model Basics [(Rails Guide)](http://guides.rubyonrails.org/active_model_basics.html)

* [SecurePassword](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword.html)
  * [ClassMethods](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html)
    * [has_secure_password](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html#method-i-has_secure_password)
  * [InstanceMethodsOnActivation](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/InstanceMethodsOnActivation.html)
    * [authenticate](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/InstanceMethodsOnActivation.html#method-i-authenticate)
* [Validations](http://api.rubyonrails.org/classes/ActiveModel/Validations.html)
  * [valid?](http://api.rubyonrails.org/classes/ActiveModel/Validations.html#method-i-valid-3F)
  * [errors](http://api.rubyonrails.org/classes/ActiveModel/Validations.html#method-i-errors)
  * [ClassMethods](http://api.rubyonrails.org/classes/ActiveModel/Validations/ClassMethods.html)
    * [validates](http://api.rubyonrails.org/classes/ActiveModel/Validations/ClassMethods.html#method-i-validates)
    * [validate](http://api.rubyonrails.org/classes/ActiveModel/Validations/ClassMethods.html#method-i-validate)
  * [Callbacks](http://api.rubyonrails.org/classes/ActiveModel/Validations/Callbacks.html)
* [Errors](http://api.rubyonrails.org/classes/ActiveModel/Errors.html)
  * [full_messages](http://api.rubyonrails.org/classes/ActiveModel/Errors.html#method-i-full_messages)


### ActionView [(DOC)](http://api.rubyonrails.org/classes/ActionView.html)

Action View Overview [(Rails Guide)](http://guides.rubyonrails.org/action_view_overview.html)

* [Helpers](http://api.rubyonrails.org/classes/ActionView/Helpers.html)
  * [TagHelper](http://api.rubyonrails.org/classes/ActionView/Helpers/TagHelper.html)
    * [content_tag](http://api.rubyonrails.org/classes/ActionView/Helpers/TagHelper.html#method-i-content_tag)
  * [UrlHelper](http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html)
    * [link_to](http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-link_to)
  * [FormTagHelper](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html)
    * [form_tag](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html#method-i-form_tag)
    * [label_tag](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html#method-i-label_tag)
    * [text_field_tag](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html#method-i-text_field_tag)
    * [submit_tag](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html#method-i-submit_tag)
    * [password_field_tag](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html#method-i-password_field_tag)
    * [hidden_field_tag](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html#method-i-hidden_field_tag)
    * [select_tag](http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html#method-i-select_tag)

Layouts and Rendering in Rails [(Rails Guide)](http://guides.rubyonrails.org/layouts_and_rendering.html)


Form Helpers [(Rails Guide)](http://guides.rubyonrails.org/form_helpers.html)

* [Helpers](http://api.rubyonrails.org/classes/ActionView/Helpers.html)
  * [FormHelper](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html)
    * [form_for](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-form_for)
    * [text_field](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-text_field)
    * [number_field](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-number_field)
    * [password_field](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-password_field)
  * [FormBuilder](http://api.rubyonrails.org/classes/ActionView/Helpers/FormBuilder.html)
    * [fields_for](http://api.rubyonrails.org/classes/ActionView/Helpers/FormBuilder.html#method-i-fields_for)
    * [hidden_field](http://api.rubyonrails.org/classes/ActionView/Helpers/FormBuilder.html#method-i-hidden_field)
    * [label](http://api.rubyonrails.org/classes/ActionView/Helpers/FormBuilder.html#method-i-label)
    * [submit](http://api.rubyonrails.org/classes/ActionView/Helpers/FormBuilder.html#method-i-submit)
  * [FormOptionsHelper](http://api.rubyonrails.org/classes/ActionView/Helpers/FormOptionsHelper.html)
    * [collection_select](http://api.rubyonrails.org/classes/ActionView/Helpers/FormOptionsHelper.html#method-i-collection_select)
    * [options_for_select](http://api.rubyonrails.org/classes/ActionView/Helpers/FormOptionsHelper.html#method-i-options_for_select)



### ActionController [(DOC)](http://api.rubyonrails.org/classes/ActionController.html)

Action Controller Overview [(Rails Guide)](http://guides.rubyonrails.org/action_controller_overview.html)


* [Redirecting](http://api.rubyonrails.org/classes/ActionController/Redirecting.html)
  * [redirect_to](http://api.rubyonrails.org/classes/ActionController/Redirecting.html#method-i-redirect_to)
  * [redirect_back](http://api.rubyonrails.org/classes/ActionController/Redirecting.html#method-i-redirect_back)

* [Parameters](http://api.rubyonrails.org/classes/ActionController/Parameters.html)
  * [require](http://api.rubyonrails.org/classes/ActionController/Parameters.html#method-i-require)
  * [permit](http://api.rubyonrails.org/classes/ActionController/Parameters.html#method-i-permit)
* [StrongParameters](http://api.rubyonrails.org/classes/ActionController/StrongParameters.html)

Rails Routing from the Outside In [(Rails Guide)](http://guides.rubyonrails.org/routing.html)

### AbstractController [(DOC)](http://api.rubyonrails.org/classes/AbstractController.html)

* [Callbacks](http://api.rubyonrails.org/classes/AbstractController/Callbacks.html)
  * [ClassMethods](http://api.rubyonrails.org/classes/AbstractController/Callbacks/ClassMethods.html)
    * [before_action](http://api.rubyonrails.org/classes/AbstractController/Callbacks/ClassMethods.html#method-i-before_action)

### ActiveSupport [(DOC)](http://api.rubyonrails.org/classes/ActiveSupport.html)

Active Support Core Extensions [(Rails Guide)](http://guides.rubyonrails.org/active_support_core_extensions.html)

* [Object](http://api.rubyonrails.org/classes/Object.html)
  * [try](http://api.rubyonrails.org/classes/Object.html#method-i-try)
* [Module](http://api.rubyonrails.org/classes/Module.html)
  * [delegate](http://api.rubyonrails.org/classes/Module.html#method-i-delegate)


### ActionDispatch [(DOC)](http://api.rubyonrails.org/classes/ActionDispatch.html)

* [Session](http://api.rubyonrails.org/classes/ActionDispatch/Session.html)
  * [CookieStore](http://api.rubyonrails.org/classes/ActionDispatch/Session/CookieStore.html)
* [Request](http://api.rubyonrails.org/classes/ActionDispatch/Request.html)
  * [fullpath](http://api.rubyonrails.org/classes/ActionDispatch/Request.html#method-i-fullpath)
  * [original_fullpath](http://api.rubyonrails.org/classes/ActionDispatch/Request.html#method-i-original_fullpath)
  * [original_url](http://api.rubyonrails.org/classes/ActionDispatch/Request.html#method-i-original_url)

* [Flash](http://api.rubyonrails.org/classes/ActionDispatch/Flash.html)
  * [FlashHash](http://api.rubyonrails.org/classes/ActionDispatch/Flash/FlashHash.html)
    * [alert](http://api.rubyonrails.org/classes/ActionDispatch/Flash/FlashHash.html#method-i-alert)
    * [notice](http://api.rubyonrails.org/classes/ActionDispatch/Flash/FlashHash.html#method-i-notice)
    * [now](http://api.rubyonrails.org/classes/ActionDispatch/Flash/FlashHash.html#method-i-now)
  * [RequestMethods](http://api.rubyonrails.org/classes/ActionDispatch/Flash/RequestMethods.html)
    * [flash](http://api.rubyonrails.org/classes/ActionDispatch/Flash/RequestMethods.html#method-i-flash)



### Others

* [Rails Generators](http://api.rubyonrails.org/classes/Rails/Generators.html)



## Gems


###  Multi-Provider Authentication

[OmniAuth](https://github.com/omniauth/omniauth)

* [List of Strategies](https://github.com/omniauth/omniauth/wiki/List-of-Strategies)
* [OmniAuth Facebook](https://github.com/mkdynamic/omniauth-facebook)
* [omniauth-google](https://github.com/Yesware/omniauth-google)

-

### Tools around Authentication

[devise](https://github.com/plataformatec/devise)

* [Instructions](http://devise.plataformatec.com.br/)

-

### Tools around Authorization

[CanCanCan](https://github.com/CanCanCommunity/cancancan)

[Pundit](https://github.com/elabs/pundit)


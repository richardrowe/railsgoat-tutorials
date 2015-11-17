# Description

Security misconfiguration can happen at any level of an application stack, including the platform, web server, application server, database, framework, and custom code. Developers and system administrators need to work together to ensure that the entire stack is configured properly. Automated scanners are useful for detecting missing patches, misconfigurations, use of default accounts, unnecessary services, etc.

# Bug

Rails has quite a few security related configurations. One of which relates to enforcing mass assignment protection.

```ruby
config.active_record.whitelist_attributes=false
```

This configuration forces an application developer to whitelist attributes that can be modified with mass-assignment. When this configuration is set to false **any attribute can be mass-assigned**.

# Hint

It has to do with mass-assignment, whitelisting and configuration.
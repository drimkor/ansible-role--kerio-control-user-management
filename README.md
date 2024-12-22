Ansible role: Kerio Control user management.
============================================

This Ansible role manages Kerio Control users via the API. It supports creating, updating, and deleting Kerio Control users.

Requirements
------------

To use this role, set to true the following environment variable: ```ANSIBLE_JINJA2_NATIVE``` or configuration key: ```jinja2_native``` in the ```[defaults]``` section of ```ansible.cfg```. This option preserves variable types during template operations.

Role Variables
--------------
Below are the available variables with their default values (refer to ```defaults/main.yml``` for details):

---
### API Connection Variables
```
kerio_control_admin_user: "Admin"
kerio_control_admin_pass: ""

kctl_port: 4081
kctl_api_url: https://{{ ansible_host }}:{{ kctl_port }}/admin/api/jsonrpc/
kctl_validate_certs: false
```

---
### Reporting settings
```
kctl_report_stdout_enable: true

kctl_report_file_enable: false
kctl_report_file_path: '~'
kctl_report_file_name: kctl_users_report
kctl_report_file_host: localhost
```
Defines the configuration options for generating and storing reports. 

The ```kctl_report_stdout_enable``` - controls whether the report is output to the standard output (console/terminal). 

The ```kctl_report_file_enable``` - determines whether the report should be saved to a file. 

If ```kctl_report_file_enable``` is enabled, specify the desired file location and name (```kctl_report_file_path``` and ```kctl_report_file_name```). The file name can contain variables such as ```kctl_users_start_timestamp```, ```kctl_users_end_timestamp```, etc.

The ```kctl_report_file_host``` indicates the system responsible for storing the report file.

---
### User Creation Policies
```
kctl_user_creating_polices_enable: true
kctl_user_pass_change_force_when_creating: true
```
These policies ensure the ```kctl_user_password_changed``` variable is set to ```true``` when creating a user. This prevents repeated updates due to the Kerio Control API always returning ```false``` for this variable.

---
### Protected Users
```
kctl_protected_users:
  - Admin
```
Defines a list of users that are excluded from processing.

---
### Domain ID
```
kctl_domain_id: "local"
```
Specifies the domain for users. This should be the same for all the users in the list.

---
### Users List
```
kctl_users:
  - {}
```
A list of Kerio Control users. Each entry is a dictionary of ```kctl_user_*``` variables. Refer to the example for usage details.

---
### User Variables
```
kctl_user_name: ""
kctl_user_password: ""
kctl_user_password_changed: false

kctl_user_full_name: ""
kctl_user_description: ""
kctl_user_email: ""
kctl_user_language: "detect"

kctl_user_auth_type: "Internal"
kctl_user_use_template: false
kctl_user_ad_enabled: true
kctl_user_local_enabled: true
kctl_user_groups: []

kctl_user_read_config: false
kctl_user_write_config: false
kctl_user_unlock_rule: false
kctl_user_dial_ras_connection: false
kctl_user_connect_vpn: false
kctl_user_use_p2p: false

kctl_user_quota_daily_enabled: false
kctl_user_quota_daily_type: "QuotaBoth"
kctl_user_quota_daily_limit: 0
kctl_user_quota_daily_units: "GigaBytes"
kctl_user_quota_weekly_enabled: false
kctl_user_quota_weekly_type: "QuotaBoth"
kctl_user_quota_weekly_limit: 0
kctl_user_quota_weekly_units: "GigaBytes"
kctl_user_quota_monthly_enabled: false
kctl_user_quota_monthly_type: "QuotaBoth"
kctl_user_quota_monthly_limit: 0
kctl_user_quota_monthly_units: "GigaBytes"
kctl_user_quota_block_traffic: false
kctl_user_quota_notify_user: false

kctl_user_www_filter_java_applet: false
kctl_user_www_filter_embed_object: false
kctl_user_www_filter_script: false
kctl_user_www_filter_popup: false
kctl_user_www_filter_referer: false

kctl_user_auto_login_addresses_enabled: false
kctl_user_auto_login_addresses_value: []
kctl_user_auto_login_address_group_enabled: false
kctl_user_auto_login_address_group_id: ""
kctl_user_auto_login_mac_addresses_enabled: false
kctl_user_auto_login_mac_addresses_value: []

kctl_user_vpn_address_enabled: false
kctl_user_vpn_address_value: ""
```
For more information on the ```kctl_user_*``` variables, refer to the official Kerio Control API documentation. To inspect API calls, use the ```kctl_user_data``` variable in ```var/main.yml```.

---

Example
-------
### Playbook example:

``` playbook.yml```
```
- name: Kerio Control 9.4.5 build 8526 API users manipulation.
  gather_facts: false
  become: false
  hosts: kerio
  roles:
    - ansible-role--kerio-control-user-management
```
```hostvar.yml```

```
kerio_control_admin_user: "Admin"
kerio_control_admin_pass: "adminpass"

kctl_report_file_enable: true

kctl_users:
  - { kctl_user_name: "example1",
      kctl_user_full_name: "Example User",
      kctl_user_password: "password", 
      kctl_user_password_changed: false,
      kctl_user_connect_vpn: true,
      kctl_user_local_enabled: true,
    }
  - { kctl_user_name: "example2", 
      kctl_user_password: "password2"
    }
```
```~/kctl_users_report```

```
Report:

Users deletion report:

  [ D ]  User: cc067446-15d3-ad4f-8448-2af62ec1f238  -  Test2  -  
  [ D ]  User: 266d1f7f-b2cd-cf44-8ca7-66fc32b94a51  -  Test5  -  
  
  [ OK ]  The users' configuration has been confirmed.

Users creation report:

  [ C ]  User: 191ab500-f9d5-e446-87df-7fc080e876fa  -  example1  -  Example User
  [ C ]  User: 415112bf-08f0-644c-81b0-b4a95e660135  -  example2  -  
  
____________________________________________________________________________________________________

All existing users on the Kerio Control host.

  User: 493c92f5-b9be-a94f-ba7a-86bc35f2539b  -  Admin  -  
  User: 191ab500-f9d5-e446-87df-7fc080e876fa  -  example1  -  Example User
  User: 415112bf-08f0-644c-81b0-b4a95e660135  -  example2  -  
  
Total: 3
____________________________________________________________________________________________________

Delete: 2   Create: 2   Update: 0   Error: 0

Start: 2024-12-22 01:27:38 (UTC)   End: 2024-12-22 01:27:47 (UTC)
____________________________________________________________________________________________________
```
Dependencies
------------
None.

License
-------
MIT

Author Information
------------------
(C) 2024 Konstantin Piatakov

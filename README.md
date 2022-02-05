gitlab
=========
Installs GitLab on any of the officially-supported x86\_64 distros.

Role Variables
--------------
| Name | Default Value |
| ---- | ------------- |
gitlab\_version | `""` |
gitlab\_edition | `gitlab-ce` |
gitlab\_initial\_root\_password | `changeme123` |
gitlab\_domain | `{{ ansible_fqdn }}` |
gitlab\_signup\_enabled | `false` |
gitlab\_configuration | `[]` |

The gitlab\_configuration variable is free-form. Its structure will become the contents of /etc/gitlab.rb. See the official [config template](https://gitlab.com/gitlab-org/omnibus-gitlab/-/blob/master/files/gitlab-config-template/gitlab.rb.template) for configuration options.

Example Playbook
----------------
``` yaml
- hosts: gitlab
  roles:
    - role: gitlab
      vars:
        gitlab_version:
        gitlab_domain: git.example.com
        gitlab_configuration:
          - gitlab_rails:
              # Email
              smtp_enable: true
              smtp_address: smtp.mailgun.org
              smtp_domain: mail.example.com
              smtp_port: 587
              smtp_authentication: plain
              smtp_enable_starttls_auto: true
              smtp_user_name: gitlab@mail.example.com
              smtp_password: 12345
              gitlab_email_from: gitlab@example.com
              gitlab_email_display_name: Example GitLab
              gitlab_email_reply_to: noreply@example.com

              # LDAP
               ldap_enabled: true
               ldap_servers: |
                 {
                   'main' => {
                     'label' => 'FreeIPA',
                     'hosts' => [
                       ['replica1.ipa.example.com', 636],
                       ['replica2.ipa.example.com', 636]
                     ],
                     'encryption' => 'simple_tls',
                     'tls_options' => {
                       'ca_file' => '/etc/ipa/ca.crt'
                     },
                     'bind_dn' => 'uid=reader,cn=sysaccounts,cn=etc,dc=ipa,dc=example,dc=com',
                     'password' => 'reader',
                     'uid' => 'uid',
                     'base' => 'cn=users,cn=accounts,dc=ipa,dc=example,dc=com',
                     'attributes' => {
                       'username' => 'uid',
                       'email' => 'mail',
                       'name' => 'cn',
                       'first_name' => 'givenName',
                       'last_name' => 'sn'
                     },
                     'user_filter' => '(objectClass=person)',
                     'allow_username_or_email_login' => true,
                     'active_directory' => false
                   }
                 }

          - nginx:
              redirect_http_to_https: true
              ssl_certificate: /etc/letsencrypt/live/{{ gitlab_domain }}/fullchain.pem
              ssl_certificate_key: /etc/letsencrypt/live/{{ gitlab_domain }}/privkey.pem
```

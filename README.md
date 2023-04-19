# postfix_send_only_relay

An [Ansible](https://www.ansible.com) role to install [Postfix](https://www.postfix.org) and configure as send-only via an SMTP relay.

This role is designed to be used on a server that will only send emails and not receive them. This is useful for recieving emails from cron jobs or other services/applications that want to send emails from a server.

## Requirements

> A SMTP relay service is required.

Your email provider may offer a SMTP relay service. If not, there are several popular SMTP relays that offer free tiers (others are also available).

| Service                            | Free Tier Email Limit       | Docs                                                                   |
| ---------------------------------- | --------------------------- | ---------------------------------------------------------------------- |
| [Mailjet](https://www.mailjet.com) | 6000 per month, 200 per day | [link](https://dev.mailjet.com/smtp-relay/overview)                    |
| [Sendgrid](https://sendgrid.com)   | 3000 per month, 100 per day | [link](https://docs.sendgrid.com/for-developers/sending-email/postfix) |

## Role Variables

```yaml
---
# Config path for postfix
postfix_config_file: "/etc/postfix/main.cf"

# The domain that is used to send emails.
postfix_mydomain:

# The domain that is used to send emails if no domain is specified. This is usually the same as mydomain or myhostname
postfix_myorigin: "$mydomain"

# mydestination controls a list of domains that postfix considers itself the final destination for.
## When aliases are set, postfix needs to "proccess" the email to then forward it on using /etc/aliases.
## example: root -> postfix -> root@example.com - > aliases -> fobar@outlook.com -> relayhost -> foobar@outlook.com
postfix_mydestination: "$myhostname, localhost.$mydomain, localhost, $mydomain"
## When aliases are not required localhost only entry will forward on all emails without any "processing" from postfix.
## example: root -> postfix -> root@example.com -> relayhost -> root@example.com
# postfix_mydestination: "localhost"

# interfaces that can send emails - loopback-only allowes only localhost to send.
postfix_inet_interfaces: "loopback-only"

# The relay host of SMTP server.
postfix_relayhost:

# Username for SMTP server.
postfix_sasl_username:
# Password for SMTP server.
postfix_sasl_password:

# The domain that the mail command will use.
postfix_mail_send_domain: "{{ postfix_mydomain }}"

# Email address alias that will recieve all emails sent to the servers root user.
postfix_root_alias:

# Email address to recieve test email when role is run.
postfix_test_send_email:
```

## Dependencies

```
community.general
```

## Example Playbook

```yaml
---
- name: Linux Admin Packages
  hosts: all
  vars:
    postfix_mydomain: "example.com"
    postfix_relayhost: "[in-v3.mailjet.com]:587"
    postfix_sasl_username: "username"
    postfix_sasl_password: "password"
    postfix_root_alias: "foobar@outlook.com"
  roles:
    - role: "dgibbs64.postfix_send_only_relay"
```

## License

MIT

## Author Information

- [Daniel Gibbs](https://danielgibbs.co.uk)

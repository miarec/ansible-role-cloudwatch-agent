# ansible-role-cloudwatch-agent
Ansible role to install amazon cloudwatch agent for monitoring instances with AWS Cloudwatch. This role can be used on non AWS instances as well.

![CI](https://github.com/miarec/ansible-role-cloudwatch-agent/actions/workflows/ci.yml/badge.svg?event=push)


Requirements
------------

None.

Role Variables
--------------

### Install options


`cwagent_verify_gpg_signature` = when true, package signature will be verified before installation, default =`true`
`cwagent_cleanup_downloads` = when true, files will be deleted after successful install, default = `true`

### Config file
`cwagent_config_file_content` = This role does not generate a config file based on supplied variables, the user is to supply the JSON formatted content the config file

[More information about teh config file](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html)

### Example collecting logs only
```yaml
cwagent_config_file_content:  |
  {
          "agent": {
                  "metrics_collection_interval": 60,
                  "run_as_user": "cwagent"
          },
          "logs": {
                  "logs_collected": {
                          "files": {
                                  "collect_list": [
                                          {
                                                  "file_path": "/var/log/syslog",
                                                  "log_group_class": "STANDARD",
                                                  "log_group_name": "syslog",
                                                  "log_stream_name": "{instance_id}",
                                                  "retention_in_days": -1
                                          },
                                          {
                                                  "file_path": "/var/log/miarec/trace/trace.log",
                                                  "log_group_class": "STANDARD",
                                                  "log_group_name": "miarec.trace",
                                                  "log_stream_name": "{instance_id}",
                                                  "retention_in_days": -1
                                          }
                                  ]
                          }
                  }
          }
  }
```

### Example with Logs and Metrics
```yaml
cwagent_config_file_content:  |
  {
          "agent": {
                  "metrics_collection_interval": 60,
                  "run_as_user": "cwagent"
          },
          "logs": {
                  "logs_collected": {
                          "files": {
                                  "collect_list": [
                                          {
                                                  "file_path": "/var/log/syslog",
                                                  "log_group_class": "STANDARD",
                                                  "log_group_name": "syslog",
                                                  "log_stream_name": "{instance_id}",
                                                  "retention_in_days": -1
                                          },
                                          {
                                                  "file_path": "/var/log/miarec/trace/trace.log",
                                                  "log_group_class": "STANDARD",
                                                  "log_group_name": "miarec.trace",
                                                  "log_stream_name": "{instance_id}",
                                                  "retention_in_days": -1
                                          }
                                  ]
                          }
                  }
          },
          "metrics": {
                  "aggregation_dimensions": [
                          [
                                  "InstanceId"
                          ]
                  ],
                  "append_dimensions": {
                          "AutoScalingGroupName": "${aws:AutoScalingGroupName}",
                          "ImageId": "${aws:ImageId}",
                          "InstanceId": "${aws:InstanceId}",
                          "InstanceType": "${aws:InstanceType}"
                  },
                  "metrics_collected": {
                          "collectd": {
                                  "metrics_aggregation_interval": 60
                          },
                          "disk": {
                                  "measurement": [
                                          "used_percent"
                                  ],
                                  "metrics_collection_interval": 60,
                                  "resources": [
                                          "*"
                                  ]
                          },
                          "mem": {
                                  "measurement": [
                                          "mem_used_percent"
                                  ],
                                  "metrics_collection_interval": 60
                          },
                          "statsd": {
                                  "metrics_aggregation_interval": 60,
                                  "metrics_collection_interval": 10,
                                  "service_address": ":8125"
                          }
                  }
          }
  }
```

Dependencies
------------

None.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - name: Install AWS Cloudwatch agent
      hosts: miarec
      become: true

      pre_tasks:
        - set_fact:
            cwagent_config_file_content:  |
              {
                      "agent": {
                              "metrics_collection_interval": 60,
                              "run_as_user": "cwagent"
                      },
                      "logs": {
                              "logs_collected": {
                                      "files": {
                                              "collect_list": [
                                                      {
                                                              "file_path": "/var/log/syslog",
                                                              "log_group_class": "STANDARD",
                                                              "log_group_name": "syslog",
                                                              "log_stream_name": "{instance_id}",
                                                              "retention_in_days": -1
                                                      }
                                              ]
                                      }
                              }
                      }
              }

      roles:
         - cloudwatch-agent

## To Do
- troubleshoot molecule test in github workflow; test pass locally but not in workflow
# streamalert-manager

[StreamAlert](https://github.com/airbnb/streamalert) is a super cool realtime event monitoring system. Unfortunately, using it requires your configuration to live in the repo alongside the code, which can create some difficulty when trying to manage a customized ruleset/config while also staying up to date with the upstream StreamAlert project. 

This project eases some of the difficulties that can cause by automating the setup process and decoupling (some) configuration from the code. 

*Warning*: Releases after v0.1.0 are targeted against the StreamAlert `release-3-0-0` branch, which is based on Python 3.x/Terraform 0.12 and changes the placement of many configuration objects.

## Requirements

Have awscli configured with admin role using `aws configure.` In addition, have the slack webhook ready.

## How to use

Update values in `vars.yml` (or create a new var file). Run ```ansible-playbook setup.yml -e @vars.yml```

## Current condition/Notes:

Rule templates will be in the templates/rules folder. The only templated fact is the slack output which will be configured as streamalert-testing1. For that reason, the playbook will setup the slack topic to as streamalert-testing1. StreamAlert automatically creates output SNS topics by default.

Running with `load_sample_config: true` will cause changes to be made to your cluster configuration. It is recommended to disable this when using this project to manage a running StreamAlert cluster.

## Build Notes

Here's how this thing works: 

__*The first playbook: "Configure StreamAlert Files"*__
1. *Clone Streamalert Repo:* Clone the repo
2. *Rule Creation Block:* Takes the rule templates and uses the var files to turn them into python rules. These are placed in rules/custom directory in the streamalert repo
3. *Config Block:* This will equate to ```./manage.py configure aws_account_id ACCOUNT_ID``` The global config files are modifired through the following process:
- Load the configs as the json files
- create a json sub-object to fill for the information (in this case, the account ID)
- Merge the json to the config to created a correctly modified config file.
4. *Virtualenv block:* This sets up the virtual environment installed with the required tools for this setup. 
5. *Create SNS Input Topic:* This will modify conf/clusters/prod.json to configure an input SNS Topic. Modification happens similar to the way it happens in step 3.
6. *Input configuration:* This portion modifies conf/sources.json in order to have cloudwatch feed into StreamAlert

__*The second playbook: "Initialize StreamAlert"*__

This one is more straightforward, it will run ```./manage.py configure prefix``` and ```./manage.py init```

__*The third playbook: "Configure Slack Output"*__

Takes in the slack secret webhook, ansible is set to the default environments to be in the StreamAlert local repository. Will run ```./manage.py build``` and ```./manage.py deploy --function all```

## Where to go from here
- Rules and rule templates are in a rudimentary state.
- The only templated fact is the slack output which will be configured as streamalert-testing1 to be a proof-of-concept. For that reason, the playbook will setup the slack topic to as streamalert-testing1.
# streamalert-manager

This project will automate the StreamAlert setup process. 

## Requirements

Have awscli configured with admin role using `aws configure.` In addition, have the slack webhook ready.

## How to use

Run the ansible playbook using ```ansible-playbook setup.yaml``` Give the information prompted. Alternatively, fill in the infomration in the ```vars.yml``` and run ```ansible-playbook setup.yaml -e vars.yml```

## Current condition/Notes:

Rule templates will be in the templates/rules folder. The only templated fact is the slack output which will be configured as streamalert-testing1. For that reason, the playbook will setup the slack topic to as streamalert-testing1. 

The project is still in the proof-of-concept phase so the rules and templating will be developed further

##Build Notes

Here's how this thing works: 

_The first playbook: "Configure StreamAlert Files"_
1. *Clone Streamalert Repo:* Clone the repo
2. *Rule Creation Block:* Takes the rule templates and uses the var files to turn them into python rules. These are placed in rules/rhythmic directory in the streamalert repo
3. *Config Block:* This will equate to ```./manage.py configure aws_account_id ACCOUNT_ID``` The global config files are modifired through the following process:
- Load the configs as the json files
- create a json sub-object to fill for the information (in this case, the account ID)
- Merge the json to the config to created a correctly modified config file.
4. *Virtualenv block:* This sets up the virtual environment installed with the required tools for this setup. 
5. *Create SNS Input Topic:* This will modify conf/clusters/prod.json to configure an input SNS Topic. Modification happens similar to the way it happens in step 3.
6. *Input configuration:* This portion modifies conf/sources.json in order to have cloudwatch feed into StreamAlert

_The second playbook: "Initialize StreamAlert"_

This one is more straightforward, it will run ```./manage.py configure prefix``` and ```./manage.py init```

_The third playbook: "Configure Slack Output"_

Takes in the slack secret webhook, ansible is set to the default environments to be in the StreamAlert local repository. Will run ```./manage.py build``` and ```./manage.py deploy --function all```




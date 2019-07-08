# streamalert-manager
This project will automate the StreamAlert setup process. 

## Requirements
<p> Have awscli configured with admin role using `aws configure.` In addition, have the slack webhook ready. </p>

## How to use
<p> Run the ansible playbook using ```ansible-playbook setup.yaml``` Give the information prompted. Alternatively,
fill in the infomration in the `vars.yml` and run ```ansible-playbook setup.yaml -e vars.yml``` </p>

## Current condition/Notes:
<p> Rule templates will be in the templates/rules folder. The only templated fact is the slack output
 which will be configured as streamalert-testing1. For that reason, the playbook will setup the slack 
 topic to as streamalert-testing1.  </p>

<p> The project is still in the proof-of-concept phase so the rules and templating will be developed further </p>

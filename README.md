# Ansible_ec2
EC2 provision

It took me a couple of hours to compile workable provision of EC2 using ansible. I found some bits and pieces none of which work from scratch in my MacOS environment. So I documented for myself all steps I had to take.

Assuming AWS account is open and in good shape...:
## 1 CreateVirtual Environment
<pre>
python3 -m venv ansi_env
source  ./bin/activate
</pre>

## 2 Install tools:
<pre>
pip install boto
pip install ansible
</pre>

## 3 Create user/group in AWS for ansible:
### a Create group (via aws console): 
<pre>
name: asw_deployer
policy: PowerUser
</pre>
### b Create ansible user
<pre>
name: asw_ansible
group: asw_deployer
</pre>
### c Generate credentials for user and place them in AWS credential file under profile like
Credetials_file in ~/.aws
<pre>
[default]
aws_access_key_id=xxx
aws_secret_access_key=xxx
[asw_ansible]
aws_access_key_id=xxx
aws_secret_access_key=xxx
</pre>
Setting profile env var to be used by ansible/boto
<pre>
export AWS_PROFILE=asw_ansible
</pre>

## 4 Make SSH AWS Key_pair:
Via console make new key_pair. Get *.pem file from AWS and place it in ~/.ssh
It will be used when Ansible talk with new EC2.
Add this key to ssh-agent to forward it( read docs if you need https://developer.github.com/v3/guides/using-ssh-agent-forwarding/)
<pre>
ssh-add ~/.ssh/my_new_key.pem 
</pre>

## 5 Create hosts file:
The ansible_python_interpreter addition should point ansible to use boto from virtual env installation. Otherwise it will be looking on machine level installation..
<pre>
[local]
localhost ansible_python_interpreter=/Users/thats_me/projects/dev_ops/ansi_env/bin/python

[webserver]
</pre>
## 6 Create YML file: (see github src) and run it as:
<pre>
ansible-playbook -i ./hosts ec2_test.yml 
</pre>

Most of the steps above were adapted from here:
https://www.agix.com.au/build-an-ec2-using-ansible-step-by-step/

## 7 Testing:
When it's all set and done on the 7th day you may test success of creation:
<pre>ansible all -i hosts -u ec2-user -m ping</pre>







  

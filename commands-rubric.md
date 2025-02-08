## RHIM

> [!ref] Reference
> - https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/identity_management_guide/users

- Kerberos tickets:
	- Authenticate
		- yourself
			- `kinit` OR `kinit <your_username>`
		- admin
			- `kinit admin`
	- List cached credentials
		- `klist`
	- Destroy cached credentials
		- `kdestroy`

- IPA
	- list a user
		- `ipa find-user <user_name> --all`
		- `ipa user-show <user_name> --all`
	- check user status (Password attempts)
		- `ipa user-status <user_name> --all`
	- reset password
		- Your own (no Admin required)
			- `passwd`
		- Someone else (Admin required)
			- `ipa user-mod <user_name> --password`
				- or
			- `ipa passwd <user_name>`
	- unlock account
		- `ipa user-unlock <user_name>`
	- extend password expiry
		- `ipa user-mod --passwordexpiration <add_2_months>`
	- ssh key
		- upload new ssh key
			- `ipa user-mod <user_name> --sshpubkey="ssh-rsa 12345abcde= ipaclient.example.com"`
		- delete ssh key
			- `ipa user-mod --sshpubkey= <user_name>`
	- enable/disable user
		- `ipa user-enable <user_name>`
		- `ipa user-diable <user_name>`

	- find group
		- `ipa group-find <group_name>`
	- show members of group
		- `ipa group-show <group_name>`
	- add user to group
		- `ipa group-add-member <group_name> --users=<username>`

	- DANGER!!
		- reinitialize replication
			- `ipa-replica-manage re-initialize --from <other_server_hostname>`

## ssh-keygen

- Generate PublicKey Fingerprint
	- `ssh-keygen -lf /path/to/key.pub`

- Generate PublicKey from PrivateKey
	- `ssh-keygen -y -f privkey.pem > pubkey.pub`

## RDS Databases

- Connect to db
	- `psql -h test-cluster-environment.cluster-cbsdf823bom.us-west-2.rds.amazonaws.com -p 5432 -U <username> -d <database_name>`

## Linux

- `cd`
```sh
home 		- cd ~ | $HOME
current 	- cd .
parent		- cd ..
relative	- cd path/to/dir
absolute 	- cd /path/to/dir
```
- `pwd`
- `ls`
	- `ls -lhaR`
```txt
-l : long output
-a : hidden files included
-h : human readable (kb, mb, gb)
-R : recursively
```
	
- `df -f` : list disk space usage
- `du -sh`
- `tail`
	- `tail -f /log/ipa.log` : continue appending output as file grows
- `nano -mql file_name`
- `grep -in -r -C 10 *.log`
	- https://medium.com/@cuncis/mastering-grep-command-your-complete-cheat-sheet-for-efficient-text-searching-in-linux-b569a573432b

### Networking info

- Check connectivity for IP/Domain on specific port (ping alternative)
	- `nc -vz domain_or_ip port`

- Gather open ports
	- lsof
		- TCP - `sudo lsof -nP -iTCP -sTCP:LISTEN`
		- UDP - `sudo lsof -nP -iUDP`
	- netstat/ss
		- `sudo netstat -tulpn`
		- `sudo ss -tulpn`

## Troubleshooting

### Bastion issues

- Get Client and Bastion IP upon connection
	- `echo "$SSH_CONNECTION"`
		or
	- `set | grep -i "SSH_CONNECTION"`

- Get debug output during ssh connection : `ssh -i <priv_key> -vv <user_name>@<ssh_host_name>`

- Check if a user's ssh session is stored in Mac
	- `ps aux | grep -i mux`
	- `ps -eaf | grep -i mux`

- Update the latest key in known_hosts
	- `ssh-keyscan -t ecdsa <host name or IP for which getting error> >> ~/.ssh/known_hosts`

### EC2 Alerts

- Get 5 largest files in a directory : `find . -follow -mount -type f -print | xargs ls -l |sort -r -n -k 5,5 | head -5`
- Get list of most recent files added in last week : `find / -type f -mmin -$((5 * 24 * 60)) -printf "%p\0" | du -h -t 1M --time --files0-from=-`

## AWS CLI

- Get EC2 instance details from IP list
```sh
aws ec2 describe-instances --filters Name=private-ip-address,Values=ipaddr1,ipaddr2,ipaddr3,... --query 'Reservations[*].Instances[*].[InstanceId, PrivateIpAddress, Tags[?Key=='Name'].Value]' --output text
```

- Find which role/credentials are being used inside an EC2 : `curl http://169.254.169.254/latest/meta-data/iam/info` OR `aws sts get-caller-identity`

- Decode Authorization Message : `aws sts decode-authorization-message --encoded-message [the_message] | jq .DecodedMessage -r | jq`

Switch between roles within an EC2 Instance : `aws sts assume-role --role-arn arn:aws:iam::ACCOUNT_ID:role/ROLE_NAME --role-session-name SESSION_NAME`

OR

```sh
CREDENTIALS=$(aws sts assume-role --role-arn arn:aws:iam::ACCOUNT_ID:role/ROLE_NAME --role-session-name SESSION_NAME)
export AWS_ACCESS_KEY_ID=$(echo $CREDENTIALS | jq -r .Credentials.AccessKeyId)
export AWS_SECRET_ACCESS_KEY=$(echo $CREDENTIALS | jq -r .Credentials.SecretAccessKey)
export AWS_SESSION_TOKEN=$(echo $CREDENTIALS | jq -r .Credentials.SessionToken)
```

To delete them use
```sh
unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
unset AWS_SESSION_TOKEN
```

Configure AWS Creds on WSL
- https://medium.com/@iam.prem/using-aws-sso-in-a-multi-account-environment-ae2c2c559bfc
- https://dev.to/hmintoh/how-to-use-multiple-aws-accounts-with-the-aws-cli-3lge
- https://dev.to/slsbytheodo/understand-the-aws-sso-login-configuration-4am7

```sh
# One time setup to configure profiles
aws configure sso-session
aws configure sso

# Set what profile to use
aws configure list-profiles
export AWS_PROFILE=<profile>

# Renew Credentials
aws sso login --profile <profile>
aws configure list
aws sts get-caller-identity
aws sso logout

# Export current profile's SSO Credentials
aws configure export-credentials --profile <profile> --format env
```

### S3

- ls files inside S3 : `aws s3 ls s3://bucket-name/path --recursive`
- Copy files matching pattern from Linux to S3 : `aws s3 cp . s3://bucket-name/path --recursive --exclude "*" --include "starts-with.*.log.gz" --dryrun`

## Git

- Re-Sign any unsigned commits again : `git rebase --exec 'git commit --amend --no-edit -n -S' -i <commit_hash>`
- Generate GPG keys and sign commits using them: https://josh-ops.com/posts/github-signing-commits/

# Research:


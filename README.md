[![Build Status](https://travis-ci.org/mirakui/ec2ssh.png?branch=master)](https://travis-ci.org/mirakui/ec2ssh)

# Introduction
ec2ssh is a ssh_config manager for Amazon EC2.

`ec2ssh` command adds `Host` descriptions to ssh_config (~/.ssh/config default). 'Name' tag of instances are used as `Host` descriptions.

# How to use
### 1. Set 'Name' tag to your instances
eg. Tag 'app-server-1' as 'Name' to an instance i-xxxxx in us-west-1 region.

### 2. Install ec2ssh

```
$ gem install ec2ssh
```

### 3. Execute `ec2ssh init`

```
$ ec2ssh init
```

### 4. Edit `.ec2ssh`

```
$ vi ~/.ec2ssh
---
path: /path/to/ssh_config
aws_keys:
  default:
    access_key_id: YOUR_ACCESS_KEY_ID
    secret_access_key: YOUR_SECRET_ACCESS_KEY
regions:
  - ap-northeast-1
```

### 5. Execute `ec2ssh update`

```
$ ec2ssh update
```
Then host-names of your instances are generated and wrote to .ssh/config

### 6. And you can ssh to your instances with your tagged name.

```
$ ssh app-server-1.us-west-1
```

# Commands
```
$ ec2ssh help [TASK]  # Describe available tasks or one specific task
$ ec2ssh init         # Add ec2ssh mark to ssh_config
$ ec2ssh update       # Update ec2 hosts list in ssh_config
$ ec2ssh remove       # Remove ec2ssh mark from ssh_config
```

## Options
### --path
Each command can use `--path` option to set ssh_config path. `~/.ssh/config` is default.

```
$ ec2ssh init --path /path/to/ssh_config
```

### --dotfile
Each command can use `--dotfile` option to set dotfile (.ec2ssh) path. `~/.ec2ssh` is default.

```
$ ec2ssh init --dotfile /path/to/ssh_config
```

### --aws-key
`ec2ssh update` allows `--aws-key` option. If you have multiple aws keys, you can choose from them as you like using this option. See Dotfile section for details.

```
$ ec2ssh update --aws-key my_key1
```


# ssh_config and mark lines
`ec2ssh init` command inserts mark lines your `.ssh/config` such as:

```
### EC2SSH BEGIN ###
# Generated by ec2ssh http://github.com/mirakui/ec2ssh
# DO NOT edit this block!
# Updated Sun Dec 05 00:00:14 +0900 2010
### EC2SSH END ###
```

`ec2ssh update` command inserts 'Host' descriptions between 'BEGIN' line and 'END' line.

```
### EC2SSH BEGIN ###
# Generated by ec2ssh http://github.com/mirakui/ec2ssh
# DO NOT edit this block!
# Updated Sun Dec 05 00:00:14 +0900 2010

# section: default
Host app-server-1.us-west-1
  HostName ec2-xxx-xxx-xxx-xxx.us-west-1.compute.amazonaws.com
Host db-server-1.ap-southeast-1
  HostName ec2-xxx-xxx-xxx-xxx.ap-southeast-1.compute.amazonaws.com
    :
    :
### EC2SSH END ###
```

`ec2ssh remove` command removes the mark lines.

# Dotfile (.ec2ssh)
Dotfile (`.ec2ssh`) is a feature which is released at v2.0.0. A template of `.ec2ssh` is created when you execute `ec2ssh init`.

```
$ ec2ssh init
$ cat ~/.ec2ssh
---
path: /home/yourname/.ssh/config
aws_keys:
  default:
    access_key_id: ...(Filled by ENV['AMAZON_ACCESS_KEY_ID']
    secret_access_key: ...(Filled by ENV['AMAZON_SECRET_ACCESS_KEY'])
regions:
  - ap-northeast-1
```

## multiple aws keys
You can use multiple aws keys at `ec2ssh update` with `--aws-key` option.

```
$ cat ~/.ec2ssh
---
path: /home/yourname/.ssh/config
aws_keys:
  default:
    access_key_id: ...
    secret_access_key: ...
  my_key1:
    access_key_id: ...
    secret_access_key: ...
regions:
  - ap-northeast-1
```

Updating ssh_config by 'default' aws key:

```
$ ec2ssh update
```

Updates ssh_config by 'my_key1' aws key:

```
$ ec2ssh update --aws-key my_key1
```

# How to upgrade from 1.x to 2.x
If you have used ec2ssh-1.x, it seems that you may not have '~/.ec2ssh'.
So you need execute `ec2ssh init` once to create `~/.ec2ssh`, and edit it as you like.

```
$ ec2ssh init
$ vi ~/.ec2ssh
```

# Notice
`ec2ssh` command updates your `.ssh/config` file default. You should make a backup of it.

# License
ec2ssh is released under the MIT license.

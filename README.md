# Alerting with ![Elastalert](https://github.com/Yelp/elastalert)
<br />
This will get you started with Alerting using Yelp's alternative to the alerting feature of X-Pack from Elastic.

### Repository structure

- <i>configurations</i> 
  - <i>config.yaml</i> - configurations file for elastalert
  - <i>smtp_auth_file.yaml</i> - authentication file for alerting via email
  - <i>zdaemon.conf</i> - configuration file for runneing elastalert as a daemon using zdaemon
  
- <i>rules</i> - contains sample configurations rules to alert on CPU, memory and disk usage

- <i>requirements.txt</i> - required python dependencies
<br />

## 1. Requirements
---

- Elasticsearch

- ISO8601 or Unix timestamped data

- Python 2.7 
<br />

## 2. Installation
---

If you're using Anaconda, do the following:

- Create a new conda environment: `conda create --name <name> python=2.7 -y`.

- Switch to the created environment: `source activate <name>`.

- Install *pip* in the same environment: `conda install pip`. This is needed because most of the Elasalert package dependencies are not present in the Continuum channels.

Refer <b>https://goo.gl/7QUSo2</b> for details on sharing a Conda environment. 
<br/ >

### Installing Elastalert

- Execute `pip install -r requirements.txt` to install the dependencies.

- Finally, run `pip install elastalert`.
<br />

## 3. Getting started 
---
 
- Create an index for ElastAlert to write to by running `elastalert-create-index` and follow the input prompts.

- Clone the Elastalert repo: `git clone https://github.com/yelp/elastalert`.
 
- Navigate to the cloned repo and create *config.yaml* file with these settings:

```yaml
rules_folder: alert_rules
run_every:
  seconds: 10
buffer_time:
  seconds: 10
#es_username: <username>
#es_password: <password>
es_host: localhost
es_port: 9200
alert_time_limit:
  days: 1
``` 

 - Create a directory called *alert_rules*. Navigate to it and create your *yaml* rule files in the same folder.
 <br />
 
## 4. Running Elastalert 
 ---

#### Testing a rule

- `elastalert-test-rule alert_rules/<your_rule_name>.yaml`.

#### Running a single rule

- `python -m elastalert.elastalert --verbose --rule <your_rule_name>.yaml`.

#### Running multiple rules

- `python -m elastalert.elastalert --verbose --config config.yaml`

This will load all the rules present in the *alert_rules* directory.
<br /><br />

## 5. Running Elastalert as a daemon
---

- Install *zdaemon*: `pip install zdaemon`. (https://goo.gl/FCww8S) 

- Create a *zdaemon.conf* file with these contents:

```conf
<runner>
  program python -m elastalert.elastalert --conf config.yaml
  socket-name /tmp/elastalert.zdsock
  forever true
</runner>
```

- To start Elastalert, execute: `zdaemon -C zdaemon.conf start`.

- To stop Elastalert, execute: `zdaemon -C zdaemon.conf stop`.
<br />

## 6. Additional configurations
---

- <b>Alerting via Email</b>

	- In the *yaml* file of the specific rule, append the following:
		  
  ```yaml
	alert:
    - email
	email: 
		- "<email-to-which-the-alert-will-be-sent>"
	smtp_host: "smtp.gmail.com" #for google email addresses
	smtp_port: 465 #for google email addresses
	smtp_ssl: true
	from_addr: "<email-from-which-the-alert-will-be-sent>"
	smtp_auth_file: "<name-of-the-authentication-file>.yaml"
  ```
      
	- Contents of the *smtp_auth_file.yaml* include user and password fields:
	    
  ```yaml
	user: "<email-address>"
	password: "<password>"
  ```
<br />

- <b>Alerting via Slack</b>

	 - In the *yaml* file of the specific rule, append the following:
	    
   ```yaml
	 alert:
      - slack
	 slack_webhook_url: "<webhook-url-of-the-slack-channel>"
	 slack_channel_override: "#<channel-name>"
	 slack_username_override: "@<user-name>"
   ```
<br />

## License

### ![MIT](../blob/master/LICENCE)
   

# Requirement

- Docker latest version (https://docs.docker.com/engine/install/ubuntu/)
- docker compose 2.9.0 :

*- sudo curl -SL https://github.com/docker/compose/releases/download/v2.9.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose*

*- sudo chmod +x /usr/local/bin/docker-compose*

**Then Verify by:**
*- docker-compose --version* (correct if output return version 2.9.)

Note: 
		
		**- run: bin/setup error occurred, then disable Module unnecessary in appdata/app/etc/config.php**
		**- run: docker-compose error occurred, then alias, run command: alias docker-compose='docker-compose'**

# Installation
- Step 1: Check docker compose: docker-compose --version
- Step 2: Git clone docker-compose: https://github.com/snaptec2020/docker-magento2.git
- Step 3: Git clone project ( clone file must be in composer/appdata)
- Step 4: Download folder vendor from server side to local (vendor must be in composer/appdata)
- Step 5: Require edit file env/env.composer
			- Set: MYSQL_DATABASE = nameproject
- Step 6: Require run command in the composer folder:

		sudo usermod -aG docker [username]
		newgrp docker
		sudo chown -R 1001 appdata
	
- Step 7: Run command: docker-compose up -d
	- for php7.2 use: docker-compose -f docker-compose7.2.yml up -d
	or 
	- bin/setup72
- Step 8: Access http://127.0.0.1
			user/pass: root/magento
			Create database with name: nameproject
- Step 9: Require run command in the composer folder:

			sudo chown -R 1001 appdata/
			sudo chmod 755 -R appdata/
- If run magento default run: bin/composer-setup 2.4.3(magento2 default version 2.4.3)
- Step 10: Require run command in the composer folder: bin/setup
- Step 11: Provide info: access magento via web browser: https://magento.test
		All information about magento, database, domain, redis,... is in the file env
- Step 12: Grant permission sql: sed 's/\sDEFINER=`[^`]*`@`[^`]*`//g' -i [file.sql]
- Step 13: Import database run: bin/mysql < [file.sql] ( file import database [file.sql] require in the composer folder)
- Step 14: Check again: https://magento.test
- If run sample data: 	

			bin/cli bin/magento sampledata:deploy
			bin/cli bin/magento setup:upgrade
------------------------
**Continue to move on to another project:**
- Step 15: Require run command in the composer folder: docker-composer down
- Step 16: Then step back --> Step 3


Note: 
		- If run: bin/setup error occurred, then disable Module unnecessary in appdata/app/etc/config.php
		- run: docker-compose error occurred, then alias, run command: alias docker-compose='docker-compose'
		 
IF ANY ISSUE, PLEASE CONTACT TO DEVOPS TEAM (slack - **Long Tran**)


.github/workflows/main.yaml
```yaml
name: cd

on:
	push:
		branches: [ main ]

jobs:
	deploy:
		runs-on: ubuntu-latest
		steps:
		  - uses: actions/checkout@v2
			with:
				token: ${{ secrets.PUSH_TOKEN  }}
		  - name: Deploy to Porduction
			uses: appleboy/ssh-action@master
			with:
				host: ${{ secrets.HOST }}
				username: ${{ secrets.USERNAME }}
				key: ${{ secrets.KEY }}
				port: ${{ secrets.PORT }}
				script: |
					cd /var/www/html
					chmod +x server_deploy.sh
					./server_deploy.sh
		  
```

server_deploy.sh
```bash
#!/bin/sh

set -e

echo "Deploying application..."

# Update Codebase
git fetch origin main
git reset --hard origin/main

# Reload PHP to update opcache
echo "" | sudo -S service php8.1-fpm reload

echo "Application Deployed"

```

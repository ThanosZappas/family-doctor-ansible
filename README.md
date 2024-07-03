## Project set up via ansible (locally)

# Change ip address of database in host_vars/gcloud-app-server.yaml to localhost 
```yaml
spring.datasource.url: jdbc:postgresql://localhost:5432/postgres
```

# Run postgres playbook to install postges and enable the database
```bash
ansible-playbook -l local-control playbooks/postgres.yaml
```

# Database details:
```yaml
name: "postgres"
user: "dbuser"
password: "pass123"
```

# Run spring playbook to install java and deploy the springboot application
```bash
ansible-playbook -l local-control playbooks/spring.yaml
```

# Access the application via http://localhost:9090/

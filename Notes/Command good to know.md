
Login to instance in private subnet from bastion

```bash
ssh -o ProxyCommand="ssh -i bastion.pem -W %h:%p ubuntu@3.127.37.209" -i prod.pem ec2-user@10.0.1.5
```


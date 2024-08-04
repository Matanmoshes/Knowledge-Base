# send_mail.py


```python
import smtplib
from email.mime.text import MIMEText

subject = "CI Pipeline Notification"
body = "Tests completed successfully"
sender = "matanmoshes66@gmail.com"  # directly use the email address
recipients = ["matan.moshe66@gmail.com"]  # replace with actual recipient emails
password = "rhmt tcgt wzyg bngq"  # directly use the app-specific password

def send_email(subject, body, sender, recipients, password):
    msg = MIMEText(body)
    msg['Subject'] = subject
    msg['From'] = sender
    msg['To'] = ', '.join(recipients)
    with smtplib.SMTP_SSL('smtp.gmail.com', 465) as smtp_server:
        smtp_server.login(sender, password)
        smtp_server.sendmail(sender, recipients, msg.as_string())
    print("Message sent!")

send_email(subject, body, sender, recipients, password)
```
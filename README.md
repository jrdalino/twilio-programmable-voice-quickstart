# Twilio Programmable Voice Quickstart
This is a simple tutorial on how to make and receive calls using Twilio and Python Flask

## Prerequisites

### Sign up for Twilio and get a phone number

### Python
```
$ python3 --version
Python 3.7.3
```
### Virtualenv
```
$ python3 -m pip install --user virtualenv
$ virtualenv --version
16.4.3
$ echo 'venv' > .gitignore
$ python3 -m venv venv
$ source venv/bin/activate
(venv) $
(venv) $ deactivate
```

### Twilio Python Helper Library
```
(venv) $ pip install twilio
```

### Flask
```
(venv) $ pip install flask
(venv) $ flask --version
Flask 1.0.2
Python 3.7.3 (default, Aug 17 2018, 19:45:58) 
[GCC 4.2.1 Compatible Apple LLVM 10.0.0 (clang-1000.0.42)]
```

## Make an Outgoing call

### Add make_call.py file
```
(venv) $ vi make_call.py
```
```
# Download the helper library from https://www.twilio.com/docs/python/install
from twilio.rest import Client


# Your Account Sid and Auth Token from twilio.com/console
# DANGER! This is insecure. See http://twil.io/secure
account_sid = 'REPLACE_ME'
auth_token = 'REPLACE_ME'
client = Client(account_sid, auth_token)

call = client.calls.create(
                        url='http://demo.twilio.com/docs/voice.xml',
                        to='+REPLACE_ME',
                        from_='+REPLACE_ME'
                    )

print(call.sid)
```

### Test Outoing call
```
(venv) $ python make_call.py
```

## Test Incoming call

### Install dependencies
```
(venv) $ vi requirements.txt
```
```
Flask>=0.12
twilio~=6.0.0
```
```
(venv) $ pip install -r requirements.txt
```

### Add answer_phone.py
```
(venv) $ vi answer_phone.py
```
```
from flask import Flask
from twilio.twiml.voice_response import VoiceResponse

app = Flask(__name__)


@app.route("/answer", methods=['GET', 'POST'])
def answer_call():
    """Respond to incoming phone calls with a brief message."""
    # Start our TwiML response
    resp = VoiceResponse()

    # Read a message aloud to the caller
    resp.say("Thank you for calling! Have a great day.", voice='alice')

    return str(resp)

if __name__ == "__main__":
    app.run(debug=True)
```

### Let's run our app
```
(venv) $ python answer_phone.py
```

### Test locally
```
(venv) $ curl http://localhost:5000/answer
```

- Response should look like this
```
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say voice="alice">Thank you for calling! Have a great day.</Say>
</Response>
```

### Download and Unzip Ngrok
- https://ngrok.com/download

### Run Ngrok and get public URL (Ex. Forwarding https://f5f635ad.ngrok.io -> localhost:5000)
```
(venv) $ ./ngrok http 5000
```

### Configure Your Webhook URL
- Log in to Twilio.com and go to the Console's Numbers page.
- Click on your voice-enabled phone number.
- Find the "Voice & Fax" section. Make sure the "Accept Incoming" selection is set to "Voice Calls." The default "Configure With" selection is what you’ll need: "Webhooks/TwiML...".
- In the "A Call Comes In" section, select "Webhook" and paste in the URL you want to use, appending your '/answer' route:

### Test your application

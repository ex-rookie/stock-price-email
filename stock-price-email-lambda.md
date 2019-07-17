import json

def lambda_handler(event, context):
    
    from botocore.vendored import requests
    import boto3
    import os
    import smtplib, ssl
    
    #Define ENV variables
    company_code = os.environ['COMPANY_CODE']
    exchange_api_key = os.environ['EXCHANGE_API_KEY']
    sender_email = os.environ['SENDER_EMAIL']
    receiver_email = os.environ['RECIPIENT_EMAIL']
    password = os.environ['PASSWORD']
    
    #Get share price data and convert to JSON for dict operations
    symbols = {'symbols': company_code}
    share_data = requests.get('https://query1.finance.yahoo.com/v7/finance/quote?lang=en-US&region=US&corsDomain=finance.yahoo.com&fields=regularMarketPrice', params=symbols)
    pricedata = share_data.json()
    
    #Get exchange-rate list and convert to JSON for dict operations
    apikey = {'app_id': exchange_api_key}
    curr_exchange = requests.get('https://openexchangerates.org/api/latest.json', params=apikey)
    exdata = curr_exchange.json()
    
    #Extract the USD price and AUD exchange-rate from the JSONs and calculate the price in AUD
    usdprice = pricedata["quoteResponse"]['result'][0]['regularMarketPrice']
    exrate = exdata['rates']['AUD']
    shareprice = str(round(usdprice * exrate, 2))

    #Email the price ("body" is optional)
    subject = 'AMZN Share Price Today: ' + shareprice + ' AUD'
    body = ''
    message = f"Subject: {subject}\n\n{body}"
    
    smtp_server = "smtp.gmail.com"
    port = 465  # For SSL

    context = ssl.create_default_context()
    
    with smtplib.SMTP_SSL(smtp_server, port, context=context) as server:
        server.login(sender_email, password)
        server.sendmail(sender_email, receiver_email, message)
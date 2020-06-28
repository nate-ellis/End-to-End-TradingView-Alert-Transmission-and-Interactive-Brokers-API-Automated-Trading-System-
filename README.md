# End-to-End-TradingView-Alert-Transmission-and-Interactive-Brokers-API-Automated-Trading-System-
This is a script (with omitted portions) that will conduct automated trading with alerts from TradingView.com and send them to an Interactive Brokers brokerageaccount


Disclaimer: In concert with the requirements to implement the system, this system WILL EXECUTE LIVE TRADES ON INTERACTIVE BROKERS (IB). 
I have purposely left code snipits out of this script to ensure that persons that clone this repo WILL READ THE IB API DOCUMENTATION
AND UNDERSTAND THEIR RISKS. Automated trading is a risk that YOU fully accept should market changes affect you adversely. 


Recognition: In developing this, I came across several sources (Obviously StackOverflow is one) but one source was crucial in the success of this development: @robswc
Please visit github.com/robswc/tradingview-webhook-bot for more details on how to the Python Flask and NGROK setup are done. 


# Assumptions

In development of this system, there are several requirements that must be met in order to begin automated trading using this script. 

1. Have a DEVELOPED and "ACCURATE" trading algorithm on TradingView.com. I will not be sharing my algos nor will this cover the development of them for yourself.
TradingView.com has several examples and prebuilt algos available for learning. 

2. Have a TradingView.com account, preferraly Premium in order to send alerts through a webhook

3. Have a funded IB account. 

4. Have an NGROK account and install the ngrok package in your Python environment (KNOW HOW TO USE NGROK)

5. Downloaded and installed the IP API (ibapi) package in your Python environment. This will require GitBash or linking the Python environment to your 
command prompt (Windows). 

6. Proper configuration of the Global Variables in you IB account to allow internal connections from 127.0.0.1.

7. Finally, the alerts on your model from TradingView must must have specific syntax in order for this Python script to properly utilize 
them and send orders. 



# The effective flow from alert to order sent to IB is as follows:

TradingView alert (synced for webhook to NGROK tunnel) --> NGROK io tunnel --> Python Flask on port 5000 --> IB account for order delivery



# Alerts

Having the alerts from TradingView setup properly is approximately 50% of the battle. The "repianting" effect can cause severe issues
with model accuracy, therefore, I utilize a multi-factor system to ensure that an ACTUAL order is complete on-time. This consists of 
developing an indicator on the model you develop and setting alerts for short and long positions from that indicator. I use this in 
tandem with the alerts on the model that TradingView.com has recently released. Additionally, in developing my model I made the model 
"lookback" and compare PREVIOUS TICKS in order to decide to go in on the CURRENT TICK. For example, I compare time[2] to time[3] instead 
of looking at indicators at time[0]. This ensures that an accurate alert is sent out. For high volumes of alerts, this may cause some 
positions to be missed but it will accurately get you in and out of whatever position you are in. 



# TradingView Alert Format

The alert system from TradingView that I use consists of 4 items:

1. An initiator alert (this instantiates the global logic variables)
2. Buy order 
3. Sell order
4. Close position (for either)

This method has been tested in the harshest conditions (over 100 trades a day) and has proven 100% accurate on paper trading in getting
you into a position and closing that position properly. The initator alert should be run first followed by the other alerts being setup
and listening on the manual indicator alert that you develop AND the model alert. The language in the alerts are (in sequence):

Initiation: {'type':'initiator', 'alert':'non-alert', 'price':'{{close}}', 'ticker':'XX'}

Buy: {'price':{{close}}, 'ticker':'XX', 'type':'buy', 'alert':'non-alert'}

Sell: {'price':{{close}}, 'ticker':'XX', 'type':'sell', 'alert':'non-alert'}

Model Alert: {'type':'out', 'alert':'alert', 'ticker':'XX', 'price':{{close}}}


Note: For the initiator, use this for the Pine Script to setup the alert. 
This is straight from github.com/robswc/tradingview-webhook-bot:

//@version=4
study("Webhook Tester")
alertcondition(true, title='Webhook Test')



# Logic Programming

Once the alert is recieved by the Python Flask on port 5000, there needs to be logic to determine the correct course of action. I have 
untilized logic gates that derive the information sent from the TradingView.com alert to determine whether the order is a buy, sell, 
or closing a position for either. Again, this is MY system and you may develop a better system to determine order composition.


# Testing 

In order to test, I built out a test script that sent out posts to the NGROK webhook then made a new Python Flask script that had no
logic gates and activated and sent orders once a post request was recieved. I then monitored my IB account (IN PAPER TRADING) to ensure
that the order was received. 




I hope you find this helpful and Good Luck!






# HP-integration-example

 'E1': 0,
 'W1': 63.0,
 'A1': 31.5,
 
 '''python
 import serial
from time import sleep
import datetime
import string
import sys

#-------------------------------------------------------
#function to send commands and print answers
def SendCommand(ID_to, command):
	#send command
	towrite = str(ID) +  str(ID_to) + command
	print "> " + str(datetime.datetime.now())[0:19] + " we sent: " + towrite
	try:
		serial.write(towrite)
	except:	#do not catch all exceptions in real code
		print "USB>Serial converter disconnected?", sys.exc_info()
		line = None
		return 0
		
	#read result
	try:
		line = serial.readline()   # read a '\n' terminated line
	except:	#do not catch all exceptions in real code
		print "USB>Serial converter disconnected?", sys.exc_info()
		line = None
		return 0

	#print answer
	if len(line) > 2:
		print "< " + str(datetime.datetime.now())[0:19] + " we got: "+ line
	else:
		print "No answer from", ID_to, "\t\t", str(datetime.datetime.now())[0:19]

#-------------------------------------------------------
#open serial
print "Starting.."
try:
	serial = serial.Serial('/dev/ttyUSB2', 9600, timeout=3)	#Change serial name to yours!
	print "Opened: ", serial.name
except serial.serialutil.SerialException:
	print "Serial open error! Change serial name to yours! (ex: /dev/ttyUSB5, or something like COM9 at win)"
	exit (0)

#-------------------------------------------------------
#some example values
eev_target = "02.50"		#new eev temperature difference
newtemp = "31.50"			#new setpoint temperature

ID 		= chr(0x30)	#this script ID, 0x30 supported by all Valden components, so do not change this value
remote_ID = chr(0x41)	#Remote Display ID ( this display https://github.com/openhp/Display  or that display https://github.com/openhp/ServiceDisplay )
					#Remote Display ID can be changed, few devices with uniq IDs can work together at the same time at same line

#-------------------------------------------------------
#example cycle
#sends 10 times (G)er all command
#then sends (T)emperature set 
#then sends (E)EV difference set
#then cycle

#commands: 
#(G)et all 
#new (T)emperature set (setpoint) 
#new (E)EV difference set

while ( 1 == 1):
	for i in xrange (10):
		SendCommand(remote_ID,"G")		#sends 0AG : from ID 0 to ID A (G)et all
		sleep (5)
	SendCommand(remote_ID,"T" + newtemp)	#sends 0AT31.50, command format TNN.NN
	sleep (5)
	SendCommand(remote_ID,"E" + eev_target)	#sends 0AE02.50, command format ENN.NN
	sleep (5)
 '''
 
 Script output example:
 ![script output screenshot](./m_script_output_screen.png)

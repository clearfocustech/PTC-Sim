# Quick install of PTC-Sim

Install a base copy of Ubuntu 20 server or desktop. This is tested on Ubuntu server 20.04. Future versions will depend on updating to Python3

## Install Python2
sudo apt-get update
sudo apt-get install python2

## Install Pip2
sudo apt-get install wget
wget https://bootstrap.pypa.io/pip/2.7/get-pip.py
sudo python2 get-pip.py

## Install Dependencies
sudo pip2 install flask
sudo pip2 install flask-googlemaps
sudo pip2 install gunicorn

## Install PTC-Sim
git clone https://github.com/clearfocustech/PTC-Sim
cd PTC-Sim

## Edit Configuration
The defaults will work fine for most, but you can edit app_config.dat

## Run PTC-Sim
Make sure you are in the PTC-Sim directory
sudo python2 ./sim-bos.py

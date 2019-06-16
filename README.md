
Install LinuxCNC from http://www.linuxcnc.org/testing-stretch-rtpreempt/linuxcnc-stretch-uspace-amd64-r13.iso

Open terminal in your home directory.

$ sudo apt-get install dist-upgrade

$ sudo apt-get install git

$ sudo apt-get install mercurial

$ git clone https://github.com/LinuxCNC/linuxcnc.git

$ git clone https://github.com/sittner/linuxcnc-ethercat.git

$ git clone https://github.com/sittner/ec-debianize.git

# 1. Build linuxcnc

$ cd linuxcnc/debian

$ ./configure uspace

$ cd .. (linuxcnc/debian$ to linuxcnc$)

$ dpkg-checkbuilddeps

$ sudo apt-get install ..... until you are done.

$ cd src (to $linuxcnc/src )

$ ./autogen.sh

$ ./configure

$ make -j4 ( j2 = dual processor speed )

$ sudo make setuid

$. ../scripts/rip-environment (to linuxcnc/src)

$linuxcnc

# 2. Build ec-debianize

$ sudo apt-get install quilt (dependency for ec-debianize)

$ cd ec-debianize

$ ./get_sourse.sh

$ cd etherlabmaster

$ dpkg-buildpackage (dpkg-checkbuilddeps optional)

(the output error "failed to sign .dsc file is no issue)

$ cd .. (to ec-debianize$)

$ sudo dpkg -i etherlabmaster_1.5.2+20180317hg9e65f7-2_amd64.deb

$ sudo dpkg -i etherlabmaster-dev_1.5.2+201803a17hg9e65f7-2_amd64.deb

$ sudo nano /etc/default/ethercat

Find your mac address:

$ ip link show (output = link/ether ....)

	Type in MASTER0_DEVICE="e8:40:f2:3e:73:b4"
	
	Type in DEVICE_MODULES="generic"
	
$ sudo update-ethercat-config (reboot, or restart your pc)

# 3. Build linuxcnc-ethercat

$ cd linuxcnc-ethercat

$ sudo make

$ sudo make install

copy lcec_conf to your linuxcnc sim map (copy it where your ini files is)

$ cp linuxcnc-ethercat/src/lcec_conf  ...

copy lcec_so to linuxcnc/rtlib

$ cp linuxcnc-ethercat/src/lcec.so linuxcnc/rtlib

# 4. Test 1

List slave

$ ethercat slaves

Create file ethercat-conf.xml

$ sudo nano ethercat-conf.xml
	<masters>  
	  <master idx="0" appTimePeriod="1000000" refClockSyncCycles="1000">
		<slave idx="0" type="EK1100" name="D1"/>
		<slave idx="1" type="EL2042" name="D2"/>
	  </master>
	</masters>

$ halrun
	show pin
	
	loadrt trivkins
	
	loadrt motmod servo_period_nsec=1000000 num_joints=3
	
	loadusr -W lcec_conf ethercat-conf.xml
	
	loadrt lcec
	
	addf lcec.read-all servo-thread
	
	addf lcec.write-all servo-thread
	
	setp lcec.0.D2.dout-0 1
	
	start
	
# 5. Test 2

Create file test.hal

$ sudo nano test.hal

	show pin
	
	loadrt trivkins
	
	loadrt motmod servo_period_nsec=1000000 num_joints=3
	
	loadusr -W lcec_conf ethercat-conf.xml
	
	loadrt lcec
	
	addf lcec.read-all servo-thread
	
	addf lcec.write-all servo-thread
	
	start

$halrun -I test.hal

	setp lcec.0.D2.dout-0 1
# 6. Error

	/dev/EtherCAT0: Permission denied
	
	$sudo chmod 7777 /dev/EtherCAT0


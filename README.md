
Install LinuxCNC 64bit from http://www.linuxcnc.org/testing-stretch-rtpreempt/linuxcnc-stretch-uspace-amd64-r13.iso

Open terminal in your home directory.

$ sudo apt-get install dist-upgrade

$ sudo apt-get install git

$ sudo apt-get install mercurial

$ git clone https://github.com/LinuxCNC/linuxcnc.git

	https://github.com/nguyenanhtucom/LinuxCNC-2.9.git

$ git clone https://github.com/sittner/linuxcnc-ethercat.git
	
	https://github.com/nguyenanhtucom/sittner-linuxcnc-ethercat.git

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

# 2. Build IgH-EtherCAT-Master_Debian-9x64

https://github.com/nguyenanhtucom/IgH-EtherCAT-Master_Debian-9x64

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
	


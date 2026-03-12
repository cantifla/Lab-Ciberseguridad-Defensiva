# Lab-Ciberseguridad-Defensiva
rácticas de seguridad en Kali Linux: Cifrado con GPG, reglas de Firewall (UFW/iptables), IDS Snort y MFA para SSH.
sudo apt update
sudo apt install gnupg2 -y
mkdir -p ~/practica_cifrado
cd ~/practica_cifrado
echo "Este es el documento secreto" > secreto.txt
gpg -c secreto.txt
rm secreto.txt
cat secreto.txt.gpg
gpg -d secreto.txt.gpg
sudo apt install apache2 vsftpd openssh-server ufw -y
sudo systemctl start apache2 vsftpd ssh
sudo iptables -A INPUT -p tcp --dport 80 -j DROP
sudo iptables -A INPUT -p tcp --dport 21 -j DROP
sudo iptables -A INPUT -p tcp --dport 22 -j DROP
sudo iptables -L
sudo iptables -F
sudo ufw enable
sudo ufw deny 80/tcp
sudo ufw deny 21/tcp
sudo ufw deny 22/tcp
sudo ufw status
sudo ufw delete deny 80/tcp
sudo ufw delete deny 21/tcp
sudo ufw delete deny 22/tcp
sudo apt -o Acquire::ForceIPv4=true install snort -y
sudo bash -c 'echo "alert icmp any any -> any any (msg:\"ALERTA: Trafico ICMP (Ping) Detectado\"; sid:1000001; rev:1;)" >> /etc/snort/rules/local.rules'
sudo bash -c 'echo "alert tcp any any -> any 21 (msg:\"ALERTA: Trafico FTP Detectado en puerto 21\"; sid:1000002; rev:1;)" >> /etc/snort/rules/local.rules'
sudo bash -c 'echo "alert tcp any any -> any 22 (msg:\"ALERTA: Trafico SSH Detectado en puerto 22\"; sid:1000003; rev:1;)" >> /etc/snort/rules/local.rules'
sudo bash -c 'echo "alert tcp any any -> any 80 (msg:\"ALERTA: Trafico HTTP Detectado en puerto 80\"; sid:1000004; rev:1;)" >> /etc/snort/rules/local.rules'
sudo snort -c /etc/snort/snort.lua -R /etc/snort/rules/local.rules -i eth0 -A alert_fast
sudo apt install libpam-google-authenticator -y
google-authenticator
sudo bash -c 'echo "auth required pam_google_authenticator.so" >> /etc/pam.d/sshd'
sudo sed -i 's/^#*KbdInteractiveAuthentication no/KbdInteractiveAuthentication yes/' /etc/ssh/sshd_config
sudo systemctl restart ssh

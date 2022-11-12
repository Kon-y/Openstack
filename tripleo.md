## build

```
wkdir="任意の場所"

cat << 'EOF' > ${wkdir}/openstack/tripleo/podmanfile
FROM centos:7
RUN yum swap -y fakesystemd systemd
RUN yum update -y

RUN yum -y install openssh-server chrony dnf sudo
RUN sed -ri 's/^UsePAM yes/UsePAM no/' /etc/ssh/sshd_config
ADD ../chrony/chrony.conf /etc/
RUN systemctl enable chronyd.service
RUN systemctl enable sshd.service
RUN ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
RUN echo 'root:aaa123' | chpasswd
RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -N ''

## undercloud
RUN useradd stack
RUN echo 'stack:stack123' | chpasswd
RUN echo "stack ALL=(root) NOPASSWD:ALL" | sudo tee -a /etc/sudoers.d/stack
RUN chmod 0440 /etc/sudoers.d/stack
RUN echo "127.0.0.1 undercloud.localdomain undercloud" >> /etc/hosts

## undercloud install
RUN yum update -y
RUN yum install -y https://trunk.rdoproject.org/centos7/current/python2-tripleo-repos-0.0.1-0.20200409224957.8bac392.el7.noarch.rpm
RUN sudo -E tripleo-repos -b train current
RUN yum install -y python-tripleoclient

USER stack
RUN cp /usr/share/python-tripleoclient/undercloud.conf.sample /home/stack/undercloud.conf
RUN sed -i 's|#enabled_hardware_types = ipmi,redfish,ilo,idrac|enabled_hardware_types = ipmi,redfish,ilo,idrac,manual-management|g' /home/stack/undercloud.conf
USER root

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
CMD /sbin/init
EOF

cd $wkdir
podman build -t dockerkon/study_osp:0.8 -f openstack/tripleo/podmanfile .
```

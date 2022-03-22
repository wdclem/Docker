FROM		debian:buster
RUN			apt-get update && DEBIAN_FRONTEDN=noninteractive &&\
			apt-get upgrade -y &&\
			apt-get install -y curl openssh-server ca-certificates perl
RUN			curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash
RUN			apt-get install -y gitlab-ce && \
			mkdir -p /etc/gitlab/ssl && chmod 755 /etc/gitlab/ssl && \
			openssl req -x509 -nodes -days 365 -newkey rsa:4096 -subj "/CN=ginlab.com" -keyout /etc/gitlab/ssl/ginlab.com.key -out /etc/gitlab/ssl/ginlab.com.crt && \
			echo "external_url \"https://ginlab.com\"" >> /etc/gitlab/gitlab.rb && \
			echo "nginx['ssl_certificate'] = \"/etc/gitlab/ssl/ginlab.com.crt\"" >> /etc/gitlab/gitlab.rb && \
			echo "nginx['ssl_certificate_key'] = \"/etc/gitlab/ssl/ginlab.com.key\"" >> /etc/gitlab/gitlab.rb && \
			echo "gitlab_rails['gitlab_shell_ssh_port'] = 8022" >> /etc/gitlab/gitlab.rb
EXPOSE		443 80 22
ENTRYPOINT	(/opt/gitlab/embedded/bin/runsvdir-start &) && gitlab-ctl reconfigure && service ssh restart && restart && gitlab-ctl tail


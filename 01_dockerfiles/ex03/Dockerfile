FROM		ubuntu:focal	
RUN			apt-get update -y && \
			apt-get upgrade -y && \
			DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
			curl \
			openssh-server \
			ca-certificates \
			postfix
RUN			mkdir -p /etc/gitlab/trusted-certs && \
			openssl req -x509 -nodes -days 365 -newkey rsa:2048 -subj '/CN=localhost' -keyout /etc/gitlab/trusted-certs/localhost.key -out /etc/gitlab/trusted-certs/localhost.crt && \
			curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash && \
			apt-get install -y gitlab-ce && \
			rm -rf /var/lib/apt/lists/* && \
			echo "external_url \"https://localhost\"" >> /etc/gitlab/gitlab.rb && \
			echo "nginx['ssl_certificate'] = \"/etc/gitlab/trusted-certs/localhost.crt\"" >> /etc/gitlab/gitlab.rb && \
			echo "nginx['ssl_certificate_key'] = \"/etc/gitlab/trusted-certs/localhost.key\"" >> /etc/gitlab/gitlab.rb && \
			echo "gitlab_rails['gitlab_shell_ssh_port'] = 4242" >> /etc/gitlab/gitlab.rb
EXPOSE		443 80 22
ENTRYPOINT	(/opt/gitlab/embedded/bin/runsvdir-start &) && gitlab-ctl reconfigure && service ssh restart &&  gitlab-ctl tail


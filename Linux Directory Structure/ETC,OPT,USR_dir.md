/etc → Stores configuration files that tell the operating system and applications how they should behave. Example: /etc/ssh/sshd_config, /etc/nginx/nginx.conf, /etc/systemd/system/myapp.service.
/usr → Stores software installed by the operating system/package manager, including executables, libraries, system commands, and shared documentation. Example: /usr/bin/git, /usr/bin/java, /usr/lib/..., /usr/share/....
/opt → Stores optional/third-party software installed manually, usually keeping the application's files together. Example: /opt/maven/bin/mvn, /opt/myapp/myapp.jar, /opt/myapp/lib/.

🧠 One-line memory trick:
------------------------------
      /etc  → CONFIGURATION
      /usr  → SYSTEM/PACKAGE-MANAGED SOFTWARE
      /opt  → MANUALLY INSTALLED/THIRD-PARTY SOFTWARE

And for systemd:
--------------------
    /usr/lib/systemd/system/ → Service files provided by packages
    /etc/systemd/system/     → Custom/admin-created service files

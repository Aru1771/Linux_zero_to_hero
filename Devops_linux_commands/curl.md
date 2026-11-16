curl is a Linux command used to send HTTP requests and check whether an application or API is responding.

Your command:

    curl -I -s -o /dev/null -w "HTTP client. -w shows timing" https://example.com

<img width="935" height="375" alt="image" src="https://github.com/user-attachments/assets/2a78a6f0-b3d5-4f48-8815-91df37be8a7d" />


Imp:

curl -v http://app_name:9090

What does -v show?

It displays details such as:

1. DNS resolution / hostname lookup.
2. IP address.
3. Connection establishment.
4. HTTP request headers.
5. HTTP response headers.
6. Connection errors, if any.


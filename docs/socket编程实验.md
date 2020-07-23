# 使用socket实现基础tcp连接

server端代码：

```c++
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <errno.h>
#include <string.h>
#include <arpa/inet.h>
#include <netinet/in.h>

#define N 64

int main( int argc, char *argv[] )
{
	int listenfd, connfd;
	struct sockaddr_in myaddr, peeraddr;
	socklen_t len;
	char buf[N] = {0};
	ssize_t n;

	if(argc < 3)
	{
		printf("Usage: %s ip port\n", argv[0]);
		return 0;
	}

	if((listenfd = socket(AF_INET, SOCK_STREAM, 0)) == -1)
	{
		perror("socket");
		exit(-1);
	}

	memset(&myaddr, 0, sizeof(myaddr));
	myaddr.sin_family = AF_INET;
	myaddr.sin_port = htons(atoi(argv[2]));
	myaddr.sin_addr.s_addr = inet_addr(argv[1]);

	if(bind(listenfd, (struct sockaddr *) &myaddr, sizeof(myaddr)) == -1)
	{
		perror("bind");
		exit(-1);
	}

	if(-1 == listen(listenfd, 5))
	{
		perror("listen");
		exit(-1);
	}


	memset(&peeraddr, 0, sizeof(peeraddr));
	len = sizeof(peeraddr);

	while(1)
	{
		if((connfd = accept(listenfd, (struct sockaddr *)&peeraddr, &len)) == -1)
		{
			perror("accept");
			exit(-1);
		}

		printf("from %s:%d\n", inet_ntoa(peeraddr.sin_addr), ntohs(peeraddr.sin_port));

		while(1)
		{
			memset(buf, 0, sizeof(buf));
			n = recv(connfd, buf, N, 0);
			if( n == 0 )
			{
				break;
			}

			buf[n] = '\0';
			printf("n=%d %s", n, buf);

			send(connfd, buf, n, 0);
		}
		close(connfd);
	}

	return 0;
}
```

client端代码：

```c++
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <errno.h>
#include <string.h>
#include <arpa/inet.h>
#include <netinet/in.h>

#define N 64

int main( int argc, char *argv[] )
{
	int sockfd;
	struct sockaddr_in servaddr, myaddr;
	char buf[N] = {0};

	if (argc < 3)
	{
		printf("usage : %s ip port\n", argv[0]);
		return 0;
	}

	if((sockfd = socket(AF_INET, SOCK_STREAM, 0)) == -1)
	{
		perror("socket");
		exit(-1);
	}


#if 0
	memset(&myaddr, 0, sizeof(myaddr));
	myaddr.sin_family = AF_INET;
	myaddr.sin_port = htons(8000);
	myaddr.sin_addr.s_addr = inet_addr(argv[1]);

	if(bind(sockfd, (struct sockaddr *) &myaddr, sizeof(myaddr)) == -1)
	{
		error("bind");
		exit(-1);
	}

#endif

	memset(&servaddr, 0, sizeof(servaddr));
	servaddr.sin_family = AF_INET;
	servaddr.sin_port = htons(atoi(argv[2]));
	servaddr.sin_addr.s_addr = inet_addr(argv[1]);

	if(connect(sockfd, (struct sockaddr *)&servaddr, sizeof(servaddr)) == -1)
	{
		perror("socket");
		exit(-1);
	}


	printf(">");
	while(fgets(buf, N, stdin) != NULL)
	{
		send(sockfd, buf, strlen(buf), 0);
		memset(buf, 0, sizeof(buf));
		recv(sockfd, buf, N, 0);
		printf("%s\n", buf);

		printf(">");
	}

	close(sockfd);

	return 0;
}
```


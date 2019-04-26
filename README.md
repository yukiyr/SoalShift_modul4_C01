LAPORAN RESMI
SISTEM OPERASI



KELOMPOK : C1

**Oleh:**

Yuki Yanuar Ratna

05111740000023

Adnan Erlangga Raharja

05111740000100

**Asisten Pembimbing:**

M.Faris Didin Andiyar

5115100118

Departemen Infomatika

Fakultas Teknologi Informasi dan Komunikasi

Institut Teknologi Sepuluh Nopember (ITS)

Surabaya

2019

**Soal**

1. Semua nama file dan folder harus terenkripsi

Enkripsi yang Atta inginkan sangat sederhana, yaitu Caesar cipher. Namun, Kusuma mengatakan enkripsi tersebut sangat mudah dipecahkan. Dia menyarankan untuk character list diekspansi,tidak hanya alfabet, dan diacak. Berikut character list yang dipakai:

```qE1~ YMUR2"`hNIdPzi%^t@(Ao:=CQ,nx4S[7mHFye#aT6+v)DfKL$r?bkOGB>}!9_wV']jcp5JZ&Xl|\8s;g<{3.u*W-0```

Misalkan ada file bernama “halo” di dalam folder “INI_FOLDER”, dan key yang dipakai adalah 17, maka:
“INI_FOLDER/halo” saat belum di-mount maka akan bernama “n,nsbZ]wio/QBE#”, saat telah di-mount maka akan otomatis terdekripsi kembali menjadi “INI_FOLDER/halo” .

Perhatian: Karakter ‘/’ adalah karakter ilegal dalam penamaan file atau folder dalam *NIX, maka dari itu dapat diabaikan


**Jawaban :**

```
#include <sys/wait.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <syslog.h>
#include <dirent.h>
#include <fcntl.h>
#include <errno.h>

int main() {
  pid_t pid, sid;

  pid = fork();

  if (pid < 0) {
    exit(EXIT_FAILURE);
  }

  if (pid > 0) {
    exit(EXIT_SUCCESS);
  }

  umask(0);

  sid = setsid();

  if (sid < 0) {
    exit(EXIT_FAILURE);
  }

  if ((chdir("/home/test")) < 0) {
    exit(EXIT_FAILURE);
  }

  close(STDIN_FILENO);
  close(STDOUT_FILENO);
  close(STDERR_FILENO);

  while(1) {
        pid_t child_id, child_id_2;
        int status;

        child_id = fork();

        if (child_id == 0) {
        // this is child

        char *argv[4] = {"mkdir", "-p", "modul2/gambar", NULL};
        execv("/bin/mkdir", argv);
        } else {
        // this is second child
        child_id_2 = fork();
        if (child_id_2 == 0) {

        char *argv[4] = {"mkdir", "-p", "modul2", NULL};
        execv("/bin/mkdir", argv);

        } else {
        while ((wait(&status)) > 0);
        char filename[10000];
        char filename2[10000];
        char filename3[10000];
        char awal1[10000];
        char akhir1[10000];
        char *pointer;
        char *pointer1;
        char *pointer2;
        char *pointer3;

        DIR *direktori;
        struct dirent *dir;
        direktori = opendir("/home/test/");
        if (direktori)
        {
                while ((dir = readdir(direktori)) != NULL)
                {
                        strcpy(filename,dir->d_name);
                        pointer3 = strrchr(filename, '.');
                        strcpy(filename3,filename);
                        if (pointer3 && (strcmp(pointer3, ".png") == 0))
                        {
                                *pointer3 = '\0';
                                strcpy(filename2,filename);
                                pointer = filename2;
                                char tambahan[10]="_grey.png";
                                strcat (pointer,tambahan);
                                char awal[]="/home/test/";
                                char akhir[]="/home/test/modul2/gambar/";
                                strcpy(awal1,awal);
                                strcpy(akhir1,akhir);
                                pointer1 = awal1;
                                pointer2 = akhir1;
                                strcat (pointer1,filename3);
                                strcat (pointer2,pointer);
                                rename (pointer1,pointer2);
                        }
                }
                closedir(direktori);
        }
      }
    }
  }
  exit(EXIT_SUCCESS);
}
```

**Kendala Yang Dialami**

-

**Screenshot**

2. Semua file video yang tersimpan secara terpecah-pecah (splitted) harus secara otomatis tergabung (joined) dan diletakkan dalam folder “Videos”

Urutan operasi dari kebutuhan ini adalah:

a. Tepat saat sebelum file system di-mount

- Secara otomatis folder “Videos” terbuat di root directory file system

- Misal ada sekumpulan file pecahan video bernama “computer.mkv.000”, “computer.mkv.001”, “computer.mkv.002”, dst. Maka secara otomatis file pecahan tersebut akan di-join menjadi file video “computer.mkv” 

- Untuk mempermudah kalian, dipastikan hanya video file saja yang terpecah menjadi beberapa file. File pecahan tersebut dijamin terletak di root folder fuse

- Karena mungkin file video sangat banyak sehingga mungkin saja saat menggabungkan file video, file system akan membutuhkan waktu yang lama untuk sukses ter-mount. Maka pastikan saat akan menggabungkan file pecahan video, file system akan membuat 1 thread/proses(fork) baru yang dikhususkan untuk menggabungkan file video tersebut

- Pindahkan seluruh file video yang sudah ter-join ke dalam folder “Videos”

- Jangan tampilkan file pecahan di direktori manapun

b. Tepat saat file system akan di-unmount

- Hapus semua file video yang berada di folder “Videos”, tapi jangan hapus file pecahan yang terdapat di root directory file system

- Hapus folder “Videos” 


**Jawaban :**

```
#include <sys/types.h>
#include <sys/stat.h>
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <errno.h>
#include <unistd.h>
#include <syslog.h>
#include <string.h>
#include <pwd.h>
#include <grp.h>

int main() {
  pid_t pid, sid;

  pid = fork();

  if (pid < 0) {
    exit(EXIT_FAILURE);
  }

  if (pid > 0) {
    exit(EXIT_SUCCESS);
  }

  umask(0);

  sid = setsid();

  if (sid < 0) {
    exit(EXIT_FAILURE);
  }

  if ((chdir("/")) < 0) {
    exit(EXIT_FAILURE);
  }

  close(STDIN_FILENO);
  close(STDOUT_FILENO);
  close(STDERR_FILENO);

  while(1) {
    int pid, pid1, pid2, pid3; 
  
    pid = fork(); 
  
    if (pid == 0) { 
  
        char *argv [4] = {"chmod", "0777", "hatiku/elen.ku", NULL};
        execv ("/bin/chmod", argv);

    } 
  
    else { 
        pid1 = fork(); 
        if (pid1 == 0) { 
            char *argv[3] = {"touch", "hatiku/elen.ku", NULL};
            execv("/usr/bin/touch", argv);

        } 
        else { 
            pid2 = fork(); 
            if (pid2 == 0) { 
                char *argv[4] = {"mkdir", "-p", "hatiku", NULL};
                execv("/bin/mkdir", argv);

            } 
  
            else { 
                                char  alamat[] = "/home/test/hatiku/elen.ku";
                                struct stat file;
                                if (stat(alamat,&file) == 0) {
                                struct group *grp;
                                struct passwd *pwd;

                                grp = getgrgid(file.st_uid);
                                pwd = getpwuid(file.st_gid);

                                if (strcmp(grp->gr_name, "www-data") != 0 && strcmp(pwd->pw_name, "www-data") != 0) {
                                        char *argv[3] = {"rm", "/home/test/hatiku/elen.ku", NULL};
                                        execv("/bin/rm", argv);
                                }
                                }
            } 
        } 
    } 
    sleep(3);
  }
  
  exit(EXIT_SUCCESS);
}
```

**Kendala Yang Dialami**

-

**Screenshot**

3. Sebelum diterapkannya file system ini, Atta pernah diserang oleh hacker LAPTOP_RUSAK yang menanamkan user bernama “chipset” dan “ic_controller” serta group “rusak” yang tidak bisa dihapus. Karena paranoid, Atta menerapkan aturan pada file system ini untuk menghapus “file bahaya” yang memiliki spesifikasi:

Owner Name 	: ‘chipset’ atau ‘ic_controller’

Group Name	: ‘rusak’

Tidak dapat dibaca

Jika ditemukan file dengan spesifikasi tersebut ketika membuka direktori, Atta akan menyimpan nama file, group ID, owner ID, dan waktu terakhir diakses dalam file “filemiris.txt” (format waktu bebas, namun harus memiliki jam menit detik dan tanggal) lalu menghapus “file bahaya” tersebut untuk mencegah serangan lanjutan dari LAPTOP_RUSAK.
        
**Jawaban :**

```
#include <sys/wait.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

int main() {
  int p[2];
  int q[2];

  pid_t child_id;
  pid_t child_id_2;
  pid_t child_id_3;
  pid_t child_id_4;
  int status;
  int r;
  char tampungan[10000]={0};

  child_id = fork();

  if (child_id == 0) {
    // this is first child
        char *argv[3] = {"touch", "daftar.txt", NULL};
        execv("/usr/bin/touch", argv);

  } else {
    // this is second child
    child_id_2 = fork();
    if (child_id_2 == 0) {
        char *argv[3] = {"unzip", "campur2.zip", NULL};
        execv("/usr/bin/unzip", argv);

    } else {
      while ((wait(&status)) > 0);

        char *ls[] = {"ls", "campur2", NULL};
        char *grep[] = {"grep", ".*.txt$", NULL};

        pipe(p);
        pipe(q);
        child_id_3=fork();
        if (child_id_3 == 0) {
                //this is third child
                dup2(p[1],1);
                close(p[0]);
                close(p[1]);
                execvp("ls", ls);
        } else {
                child_id_4 = fork();
                if (child_id_4 == 0) {
                        //this is fourth child
                        dup2(p[0],0);
                        dup2(q[1],1);
                        close(p[1]);
                        close(p[0]);
                        close(q[1]);
                        close(q[0]);
                        execvp("grep", grep);
                } else {
                        close(p[0]);
                        close(p[1]);
                        close(q[1]);
                        r = read(q[0],tampungan,sizeof(tampungan));
                        FILE *out_file = fopen("daftar.txt","w+");
                        fprintf(out_file,"%.*s\n", r, tampungan);
                }
        }
    }
  }
}
```

**Kendala Yang Dialami**

-

**Screenshot**

4.  Pada folder YOUTUBER, setiap membuat folder permission foldernya akan otomatis menjadi 750. Juga ketika membuat file permissionnya akan otomatis menjadi 640 dan ekstensi filenya akan bertambah “.iz1”. File berekstensi “.iz1” tidak bisa diubah permissionnya dan memunculkan error bertuliskan “File ekstensi iz1 tidak boleh diubah permissionnya.”

**Jawaban :**

```
#include <sys/types.h>
#include <sys/stat.h>
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <errno.h>
#include <unistd.h>
#include <syslog.h>
#include <string.h>
#include <time.h>

int main() {
  pid_t pid, sid;

  pid = fork();

  if (pid < 0) {
    exit(EXIT_FAILURE);
  }

  if (pid > 0) {
    exit(EXIT_SUCCESS);
  }

  umask(0);

  sid = setsid();

  if (sid < 0) {
    exit(EXIT_FAILURE);
  }

  if ((chdir("/")) < 0) {
    exit(EXIT_FAILURE);
  }

  close(STDIN_FILENO);
  close(STDOUT_FILENO);
  close(STDERR_FILENO);
  int angka = 1;
  while(1) {
    struct stat baca;

    time_t raw;
    time(&raw);

    char file[100], sehat[100];
    strcpy(file, "/home/elang/Documents/makanan/makan_enak.txt");

    stat(file, &baca);

    int last = (int)difftime(raw, baca.st_atime);

    if (last <= 30)
    {
      sprintf(sehat, "/home/elang/Documents/makanan/makan_sehat%d.txt", angka);
      FILE *fsehat;
      fsehat = fopen(sehat, "w");
      fclose(fsehat);
      angka++;
    }
    sleep(5);
  }

  exit(EXIT_SUCCESS);
}
```

**Kendala Yang Dialami**

-

**Screenshot**

5. Ketika mengedit suatu file dan melakukan save, maka akan terbuat folder baru bernama Backup kemudian hasil dari save tersebut akan disimpan pada backup dengan nama namafile_[timestamp].ekstensi. Dan ketika file asli dihapus, maka akan dibuat folder bernama RecycleBin, kemudian file yang dihapus beserta semua backup dari file yang dihapus tersebut (jika ada) di zip dengan nama namafile_deleted_[timestamp].zip dan ditaruh ke dalam folder RecycleBin (file asli dan backup terhapus). Dengan format [timestamp] adalah yyyy-MM-dd_HH:mm:ss

**Jawaban :**

```
#include <sys/types.h>
#include <sys/stat.h>
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <errno.h>
#include <unistd.h>
#include <syslog.h>
#include <string.h>
#include <time.h>

int main() {
    pid_t pid, sid;
    pid = fork();
    if (pid < 0) {
        exit(EXIT_FAILURE);
    }
    if (pid > 0) {
        exit(EXIT_SUCCESS);
    }
    umask(0);
    sid = setsid();
    if (sid < 0) {
        exit(EXIT_FAILURE);
    }
    if ((chdir("/")) < 0) {
        exit(EXIT_FAILURE);
    }
    close(STDIN_FILENO);
    close(STDOUT_FILENO);
    close(STDERR_FILENO);
    int angka = 1;
    while(1) {
        time_t raw;
        struct tm *timeinfo;
        char tanggal[40], path[80];
        time(&raw);
        timeinfo = localtime(&raw);
        strftime(tanggal, sizeof(tanggal), "%d:%m:%Y-%H:%M", timeinfo); 
        sprintf(path, "/home/elang/log/%s/", tanggal);
        mkdir(path, S_IRWXU | S_IRWXO | S_IRWXG);
        
        for(int i = 0; i < 30 ; i++){
            char file[50], path2[80];
            strcpy(path2,path);
            sprintf(file, "log%d.log", angka);
            strcat(path2, file);

            FILE *fwrite, *fread;
            fread = fopen("/var/log/syslog", "r");
            fwrite = fopen(path2,"w+");

            char ch;

            if(fread != NULL && fwrite != NULL){
            while ((ch = fgetc(fread)) != EOF)
                fputc(ch, fwrite);

            fclose(fwrite);
            }
            angka++;
            sleep(60);
        }

    }
    exit(EXIT_SUCCESS);
}
```

**Kendala Yang Dialami**-

-

**Screenshot**



**KODINGAN PENUH**

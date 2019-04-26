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
char chlist[] = {"qE1~ YMUR2\"`hNIdPzi%^t@(Ao:=CQ,nx4S[7mHFye#aT6+v)DfKL$r?bkOGB>}!9_wV']jcp5JZ&Xl|\\8s;g<{3.u*W-0"}, path1[1024], path2[1024];

void enc(char arr[]);
void dec(char arr[]);

void enc(char arr[])
{
	int i = 0, j, k;
	while(arr[i] != '\0')
	{
		k = arr[i];

		if(k == '/'){
			j = 0;
			while(j < strlen(chlist))
			{
				if(k == chlist[j]) break;
				j++;
			}
			
			j = j + 17;
			if(j > 93)
				j = j - strlen(chlist);

			arr[i] = chlist[j];
		}

		++i;
	}
}

void dec(char arr[])
{
	int i = 0, j, k;
	while(arr[i] != '\0')
	{
		k = arr[i];

		if(k == '/'){
			j = 0;
			while(j < strlen(chlist))
			{
				if(k == chlist[i]) break;
				j++;
			}
			
			j = j - 17;

			if(j < 0)
				j = j + strlen(chlist);

			arr[i] = chlist[j];
		}

		++i;
	}
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

**Kendala Yang Dialami**

Tidak sempat mengerjakan

**Screenshot**

3. Sebelum diterapkannya file system ini, Atta pernah diserang oleh hacker LAPTOP_RUSAK yang menanamkan user bernama “chipset” dan “ic_controller” serta group “rusak” yang tidak bisa dihapus. Karena paranoid, Atta menerapkan aturan pada file system ini untuk menghapus “file bahaya” yang memiliki spesifikasi:

Owner Name 	: ‘chipset’ atau ‘ic_controller’

Group Name	: ‘rusak’

Tidak dapat dibaca

Jika ditemukan file dengan spesifikasi tersebut ketika membuka direktori, Atta akan menyimpan nama file, group ID, owner ID, dan waktu terakhir diakses dalam file “filemiris.txt” (format waktu bebas, namun harus memiliki jam menit detik dan tanggal) lalu menghapus “file bahaya” tersebut untuk mencegah serangan lanjutan dari LAPTOP_RUSAK.
        
**Jawaban :**

```
static int xmp_open(const char *path, struct fuse_file_info *fi)
{
        int res;
        char copy[1000];
        (void) fi;

        sprintf(copy,"%s%s",dirpath,path);

        res = open(copy,fi->flags);
        if(res==-1)
        {
                res = -errno;
        }
        else {
                res = open(copy,fi->flags);
                DIR *direktori;
                struct dirent *dir;
                direktori = opendir(copy);
                if(direktori)
                {
                        while ((dir = readdir(direktori)) != NULL)
                        {
                                struct stat file;
                                if (stat(copy,&file) == 0) {
                                        struct group *grp;
                                        struct passwd *pwd;

                                        grp = getgrgid(file.st_uid);
                                        pwd = getpwuid(file.st_gid);

                                        if (strcmp(grp->gr_name, "rusak") != 0 && (strcmp(pwd->pw_name, "chipset") != 0 || strcmp(pwd->pw_name, "ic_controller")) != 0) {
                                                FILE *out_file = fopen("filemiris.txt","w+");
                                                fprintf(out_file,"%s %d %d %ld\n", dir->d_name, file.st_uid, file.st_gid, file.st_atime);
                                                remove(dir->d_name);
                                                return -errno;
                                        }
                                }
                        }
                }
                if(res==-1) res=-errno;
        }
        return res;
}

```

**Kendala Yang Dialami**

-

**Screenshot**

4.  Pada folder YOUTUBER, setiap membuat folder permission foldernya akan otomatis menjadi 750. Juga ketika membuat file permissionnya akan otomatis menjadi 640 dan ekstensi filenya akan bertambah “.iz1”. File berekstensi “.iz1” tidak bisa diubah permissionnya dan memunculkan error bertuliskan “File ekstensi iz1 tidak boleh diubah permissionnya.”

**Jawaban :**

```
static int xmp_mkdir(const char *path, mode_t mode)
{
        int res;
        char fpath[1000];
        sprintf(fpath, "%s%s", dirpath, path);

        res = mkdir(fpath,750);

        if(res==-1)
        {
                return -errno;
        }
        return 0;
}

static int xmp_create(const char *path, mode_t mode, struct fuse_file_info *fi)
{
        int res, res2;
        char new_from[1000];
        char new_to[1000];

        res =  open(path, fi->flags, 640);
        if (res == -1)
        {
                return -errno;
        }
        fi->fh = res;
        sprintf(new_from,"%s%s", dirpath,path);
        sprintf(new_to,"%s%s.iz1", dirpath,path);
        res2 = rename(new_from,new_to);
        if(res2==-1)
        {
                return -errno;
        }

        return 0;
}

static int xmp_chmod (const char *path, mode_t mode)
{
        int res;
        char copy[1000];
        sprintf(copy,"%s%s",dirpath,path);

        char tampungan[1000];
        int i,j;
        res = chmod(path,mode);
        for(i=0;i<strlen(copy) && (copy[i] != '.' && copy[i+1] != 'i');i++);
        strcpy(tampungan,copy+i);
        for(j=0;j<1;++j)
        {
                if(!strcmp(tampungan,ext[j]))
                {
                        char *argv[5] = {"zenity", "--error", "--title=Error", "--text=File ekstensi iz1 tidak boleh diubah permisionnya", NULL};
                        execv("/usr/bin/zenity", argv);

                        return -errno;
                }
        }

        if(res==-1)
                return -errno;
        return 0;
}
```

**Kendala Yang Dialami**

-

**Screenshot**

5. Ketika mengedit suatu file dan melakukan save, maka akan terbuat folder baru bernama Backup kemudian hasil dari save tersebut akan disimpan pada backup dengan nama namafile_[timestamp].ekstensi. Dan ketika file asli dihapus, maka akan dibuat folder bernama RecycleBin, kemudian file yang dihapus beserta semua backup dari file yang dihapus tersebut (jika ada) di zip dengan nama namafile_deleted_[timestamp].zip dan ditaruh ke dalam folder RecycleBin (file asli dan backup terhapus). Dengan format [timestamp] adalah yyyy-MM-dd_HH:mm:ss

**Jawaban :**

```
static int xmp_open(const char *path, struct fuse_file_info *fi)
{
        int res;
        char copy[1000];
        (void) fi;

        sprintf(copy,"%s%s",dirpath,path);

        res = open(copy,fi->flags);
        if(res==-1)
        {
                res = -errno;
        }
        else {
                res = open(copy,fi->flags);
                DIR *direktori;
                struct dirent *dir;
                direktori = opendir(copy);
                if(direktori)
                {
                        while ((dir = readdir(direktori)) != NULL)
                        {
                                char tanggal[40];
                                time_t ltime;
                                struct tm *timeinfo;
                                time(&ltime);
                                timeinfo = localtime(&ltime);
                                strftime(tanggal, sizeof(tanggal), "%Y:%m:%d_d:%H:%M:%S", timeinfo); 
                                FILE *fromfile = fopen(dir->d_name, "w+");
                                mkdir("Backup",0777);
                                FILE *tofile = fopen("/home/test/Backup/dir->d_name", "w");
                                if(remove(dir->d_name)==0)
                                {
                                        mkdir("RecycleBin",0777);
                                        char *argv[5] = {"zip", "/home/test/Shift4/dir->d_name", "/home/test/Backup/dir->d_name", "dir->d_name_deleted_tm->tm_year+1900-tm->tm_mon-tm->tm_mday_tm->tm_hour:$
                                        execv("/usr/bin/zip", argv);
                                }
                        }
                }
                return -errno;
        }
        return 0;
}

```

**Kendala Yang Dialami**-

praktikum3.c: In function ‘xmp_open’:
praktikum3.c:117:25: warning: unused variable ‘tofile’ [-Wunused-variable]
                   FILE *tofile = fopen("/home/test/Backup/dir->d_name", "w");
                         ^~~~~~
praktikum3.c:115:25: warning: unused variable ‘fromfile’ [-Wunused-variable]
                   FILE *fromfile = fopen(dir->d_name, "w+");

**Screenshot**



**KODINGAN PENUH**

```
#define FUSE_USE_VERSION 28
#include <fuse.h>
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <dirent.h>
#include <errno.h>
#include <sys/time.h>

static const char *dirpath = "/home/[user]/Documents";
char chlist[] = {"qE1~ YMUR2\"`hNIdPzi%^t@(Ao:=CQ,nx4S[7mHFye#aT6+v)DfKL$r?bkOGB>}!9_wV']jcp5JZ&Xl|\\8s;g<{3.u*W-0"}, path1[1024], path2[1024];

void enc(char arr[]);
void dec(char arr[]);

static int xmp_getattr(const char *path, struct stat *stbuf)
{
  int res;
        char fpath[1000];
        sprintf(fpath,"%s%s",dirpath,path);
        res = lstat(fpath, stbuf);

        if (res == -1)
                return -errno;

        return 0;
}

static int xmp_readdir(const char *path, void *buf, fuse_fill_dir_t filler,
                       off_t offset, struct fuse_file_info *fi)
{
  char fpath[1000];
        if(strcmp(path,"/") == 0)
        {
                path=dirpath;
                sprintf(fpath,"%s",path);
        }
        else sprintf(fpath, "%s%s",dirpath,path);
        int res = 0;

        DIR *dp;
        struct dirent *de;

        (void) offset;
        (void) fi;

        dp = opendir(fpath);
        if (dp == NULL)
                return -errno;

        while ((de = readdir(dp)) != NULL) {
                struct stat st;
                memset(&st, 0, sizeof(st));
                st.st_ino = de->d_ino;
                st.st_mode = de->d_type << 12;
                res = (filler(buf, de->d_name, &st, 0));
                        if(res!=0) break;
        }

        closedir(dp);
        return 0;
}

static int xmp_read(const char *path, char *buf, size_t size, off_t offset,
                    struct fuse_file_info *fi)
{
  char fpath[1000];
        if(strcmp(path,"/") == 0)
        {
                path=dirpath;
                sprintf(fpath,"%s",path);
        }
        else sprintf(fpath, "%s%s",dirpath,path);
        int res = 0;
  int fd = 0 ;

        (void) fi;
        fd = open(fpath, O_RDONLY);
        if (fd == -1)
                return -errno;

        res = pread(fd, buf, size, offset);
        if (res == -1)
                res = -errno;

        close(fd);
        return res;
}

static int xmp_mkdir(const char *path, mode_t mode)
{
        int res;
        char fpath[1000];
        sprintf(fpath, "%s%s", dirpath, path);

        res = mkdir(fpath,750);

        if(res==-1)
        {
                return -errno;
        }
        return 0;
}

static int xmp_create(const char *path, mode_t mode, struct fuse_file_info *fi)
{
        int res, res2;
        char new_from[1000];
        char new_to[1000];

        res =  open(path, fi->flags, 640);
        if (res == -1)
        {
                return -errno;
        }
        fi->fh = res;
        sprintf(new_from,"%s%s", dirpath,path);
        sprintf(new_to,"%s%s.iz1", dirpath,path);
        res2 = rename(new_from,new_to);
        if(res2==-1)
        {
                return -errno;
        }

        return 0;
}

static int xmp_chmod (const char *path, mode_t mode)
{
        int res;
        char copy[1000];
        sprintf(copy,"%s%s",dirpath,path);

        char tampungan[1000];
        int i,j;
        res = chmod(path,mode);
        for(i=0;i<strlen(copy) && (copy[i] != '.' && copy[i+1] != 'i');i++);
        strcpy(tampungan,copy+i);
        for(j=0;j<1;++j)
        {
                if(!strcmp(tampungan,ext[j]))
                {
                        char *argv[5] = {"zenity", "--error", "--title=Error", "--text=File ekstensi iz1 tidak boleh diubah permisionnya", NULL};
                        execv("/usr/bin/zenity", argv);

                        return -errno;
                }
        }

        if(res==-1)
                return -errno;
        return 0;
}

static int xmp_open(const char *path, struct fuse_file_info *fi)
{
        int res;
        char copy[1000];
        (void) fi;

        sprintf(copy,"%s%s",dirpath,path);

        res = open(copy,fi->flags);
        if(res==-1)
        {
                res = -errno;
        }
        else {
                res = open(copy,fi->flags);
                DIR *direktori;
                struct dirent *dir;
                direktori = opendir(copy);
                if(direktori)
                {
                        while ((dir = readdir(direktori)) != NULL)
                        {
                                struct stat file;
                                if (stat(copy,&file) == 0) {
                                        struct group *grp;
                                        struct passwd *pwd;

                                        grp = getgrgid(file.st_uid);
                                        pwd = getpwuid(file.st_gid);

                                        if (strcmp(grp->gr_name, "rusak") != 0 && (strcmp(pwd->pw_name, "chipset") != 0 || strcmp(pwd->pw_name, "ic_controller")) != 0) {
                                                FILE *out_file = fopen("filemiris.txt","w+");
                                                fprintf(out_file,"%s %d %d %ld\n", dir->d_name, file.st_uid, file.st_gid, file.st_atime);
                                                remove(dir->d_name);
                                        } 
                                 }
                                 char tanggal[40];
                                 time_t ltime;
                                 struct tm *timeinfo;
                                 time(&ltime);
                                 timeinfo = localtime(&ltime);
                                 strftime(tanggal, sizeof(tanggal), "%Y:%m:%d_d:%H:%M:%S", timeinfo); 
                                 FILE *fromfile = fopen(dir->d_name, "w+");
                                 mkdir("Backup",0777);
                                 FILE *tofile = fopen("/home/test/Backup/dir->d_name", "w");
                                 if(remove(dir->d_name)==0)
                                 {
                                                                mkdir("RecycleBin",0777);
                                                                char *argv[5] = {"zip", "/home/test/Shift4/dir->d_name", "/home/test/Backup/dir->d_name", "dir->d_name_deleted_tm->tm_year+1900-tm->tm_mon-tm->tm_mday_tm->tm_hour:$
                                                                execv("/usr/bin/zip", argv);
                                 }
                        }
                }
                return -errno;
        }
        return 0;
}

void enc(char arr[])
{
	int i = 0, j, k;
	while(arr[i] != '\0')
	{
		k = arr[i];

		if(k == '/'){
			j = 0;
			while(j < strlen(chlist))
			{
				if(k == chlist[j]) break;
				j++;
			}
			
			j = j + 17;
			if(j > 93)
				j = j - strlen(chlist);

			arr[i] = chlist[j];
		}

		++i;
	}
}

void dec(char arr[])
{
	int i = 0, j, k;
	while(arr[i] != '\0')
	{
		k = arr[i];

		if(k == '/'){
			j = 0;
			while(j < strlen(chlist))
			{
				if(k == chlist[i]) break;
				j++;
			}
			
			j = j - 17;

			if(j < 0)
				j = j + strlen(chlist);

			arr[i] = chlist[j];
		}

		++i;
	}
}

static struct fuse_operations xmp_oper = {
        .getattr        = xmp_getattr,
        .readdir        = xmp_readdir,
        .read           = xmp_read,
        .mkdir          = xmp_mkdir,
        .create         = xmp_create,
        .chmod          = xmp_chmod,
        .open           = xmp_open
};

int main(int argc, char *argv[])
{
        umask(0);
        return fuse_main(argc, argv, &xmp_oper, NULL);
}
```

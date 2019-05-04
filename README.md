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
char caesar[] = {"qE1~ YMUR2\"`hNIdPzi%^t@(Ao:=CQ,nx4S[7mHFye#aT6+v)DfKL$r?bkOGB>}!9_wV']jcp5JZ&Xl|\\8s;g<{3.u*W-0"};
char encrypt[1000], decrypt[1000];

void enc(char arr[]);
void dec(char arr[]);

void enc(char arr[])
{
	int n, i, ch;
	for(n = 0; arr[n] != '\0'; ++n)
	{
		ch = arr[n];

		if(ch == '/') continue;

		for(i = 0; i < strlen(caesar); i++)
		{
			if(ch == caesar[i]) break;
		}
		
		i = i + 17;
		if(i > 93) i = i - strlen(caesar);

		arr[n] = caesar[i];
	}
}

void dec(char arr[])
{
	int n, i, ch;
	for(n = 0; arr[n] != '\0'; ++n)
	{
		ch = arr[n];

		if(ch == '/') continue;

		for(i = 0; i < strlen(caesar); i++)
		{
			if(ch == caesar[i]) break;
		}
		
		i = i - 17;
		if(i < 0) i = i + strlen(caesar);

		arr[n] = caesar[i];
	}
}
```

**Kendala Yang Dialami**

-

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

3. Sebelum diterapkannya file system ini, Atta pernah diserang oleh hacker LAPTOP_RUSAK yang menanamkan user bernama “chipset” dan “ic_controller” serta group “rusak” yang tidak bisa dihapus. Karena paranoid, Atta menerapkan aturan pada file system ini untuk menghapus “file bahaya” yang memiliki spesifikasi:

Owner Name 	: ‘chipset’ atau ‘ic_controller’

Group Name	: ‘rusak’

Tidak dapat dibaca

Jika ditemukan file dengan spesifikasi tersebut ketika membuka direktori, Atta akan menyimpan nama file, group ID, owner ID, dan waktu terakhir diakses dalam file “filemiris.txt” (format waktu bebas, namun harus memiliki jam menit detik dan tanggal) lalu menghapus “file bahaya” tersebut untuk mencegah serangan lanjutan dari LAPTOP_RUSAK.
        
**Jawaban :**

```
void checkOwnerGroup(const char *path, const char *cpath);

void checkOwnerGroup(const char *path, const char *cpath)
{
	char copy[1000];
	sprintf(copy,"%s%s",dirpath,path);
	struct stat file;
        lstat(path,&file);
        struct group *grp;
        struct passwd *pwd;

        grp = getgrgid(file.st_uid);
        pwd = getpwuid(file.st_gid);

        if (strcmp(grp->gr_name, "rusak")!=0 && (strcmp(pwd->pw_name, "chipset")!=0 || !strcmp(pwd->pw_name, "ic_controller")!=0) && access(path,R_OK) !=0))
	{
		char filemiris[]="/filemiris.txt";
		char newfile[1000];
		sprintf(newfile,"%s%s",dirpath,filemiris);
        	FILE *out_file = fopen(newfile,"a");
        	fprintf(out_file,"%s %d %d %ld %s %s\n", cpath, file.st_uid, file.st_gid, file.st_atime, grp->gr_name, pwd->pw_name);
		fclose(out_file);
        	remove(path);
        }
}

```

**Kendala Yang Dialami**

-

4.  Pada folder YOUTUBER, setiap membuat folder permission foldernya akan otomatis menjadi 750. Juga ketika membuat file permissionnya akan otomatis menjadi 640 dan ekstensi filenya akan bertambah “.iz1”. File berekstensi “.iz1” tidak bisa diubah permissionnya dan memunculkan error bertuliskan “File ekstensi iz1 tidak boleh diubah permissionnya.”

**Jawaban :**

```
static int xmp_mkdir(const char *path, mode_t mode)
{
        int res;
        char fpath[1000];
	char cpath[1000];
	char youtuber[1000]="/YOUTUBER";
	sprintf(cpath, "%s", path);
        sprintf(fpath, "%s%s", dirpath,cpath);

	if(strstr(fpath,youtuber))
	{
		res = mkdir(fpath,0750);
	}
	else
	{
		res = mkdir(fpath,mode);
	}

        if(res==-1)
        {
                return -errno;
        }
        return 0;
}

static int xmp_create(const char *path, mode_t mode, struct fuse_file_info *fi)
{
	(void) fi;
        int res;
        char fpath[1000];
	char cpath[1000];
	char youtuber[1000]="/YOUTUBER";
	sprintf(cpath, "%s", path);
        sprintf(fpath, "%s%s", dirpath, cpath);

	if(strstr(fpath,youtuber))
	{
		res = creat(fpath,0640);
	}
	else
	{
		res = creat(fpath,mode);
	}

        if(res==-1)
        {
                return -errno;
        }
	close(res);
        return 0;
}

static int xmp_chmod (const char *path, mode_t mode)
{
        int res;
        char fpath[1000];
	char cpath[1000];
	char youtuber[1000]="/YOUTUBER";
	sprintf(cpath, "%s", path);
        sprintf(fpath, "%s%s", dirpath, cpath);

	if(strstr(fpath,youtuber))
	{
		int status;
		pid_t child1;
		child1= fork();
		if (child1==0)
		{
			char *argv[5] = {"zenity", "--error", "--title=Error", "--text=File ekstensi iz1 tidak boleh diubah permisionnya", NULL};
                	execv("/usr/bin/zenity", argv);
		}
		else
		{
			while(wait(&status)>0);
		}
	}
	else
	{
		res = chmod(fpath,mode);
	}

        if(res==-1)
        {
                return -errno;
        }
        return 0;
}

```

**Kendala Yang Dialami**

-

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
                                //FILE *fromfile = fopen(dir->d_name, "w+");
                                mkdir("Backup",0777);
                                //FILE *tofile = fopen("/home/test/Backup/dir->d_name", "w");
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

masih salah, tidak menjalankan fungsi tersebut sama sekali

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
#include <pwd.h>
#include <grp.h>
#include <time.h>

static const char *dirpath = "/home/elang/modul4";
char caesar[] = {"qE1~ YMUR2\"`hNIdPzi%^t@(Ao:=CQ,nx4S[7mHFye#aT6+v)DfKL$r?bkOGB>}!9_wV']jcp5JZ&Xl|\\8s;g<{3.u*W-0"};
char encrypt[1000], decrypt[1000];

void enc(char arr[]);
void dec(char arr[]);
void checkOwnerGroup(const char *path, const char *cpath);

static int xmp_getattr(const char *path, struct stat *stbuf)
{
  	int res;
	char fpath[1000];
	strcpy(encrypt, path);
	if(strcmp(encrypt, ".") != 0 && strcmp(encrypt, "..") != 0)
		enc(encrypt);

	sprintf(fpath,"%s%s", dirpath, encrypt);
	res = lstat(fpath, stbuf);
	if (res == -1)
		return -errno;

	return 0;
}

static int xmp_readdir(const char *path, void *buf, fuse_fill_dir_t filler,
		       off_t offset, struct fuse_file_info *fi)
{
  	char fpath[1000];
  	char youtuber[100]="/YOUTUBER";
	if(strcmp(path,"/") == 0)
	{
		path=dirpath;
		sprintf(fpath,"%s",path);
	}
	else
	{
		strcpy(encrypt, path);
		if(strcmp(encrypt, ".") != 0 && strcmp(encrypt, "..") != 0)
			enc(encrypt);
		sprintf(fpath, "%s%s", dirpath, encrypt);
	}
	int res = 0;

	DIR *dp;
	struct dirent *de;

	(void) offset;
	(void) fi;

	dp = opendir(fpath);
	if (dp == NULL)
		return -errno;

	while ((de = readdir(dp)) != NULL)
	{
		struct stat st;
		memset(&st, 0, sizeof(st));

		char show[2000];
		snprintf(show, 2000, "%s/%s", fpath, de->d_name);
		stat(show, &st);

		st.st_ino = de->d_ino;
		st.st_mode = de->d_type << 12;

		strcpy(decrypt, de->d_name);
		printf("directory : %s\n",show);

		char fullpath[1000];
		sprintf(fullpath,"%s%s", fpath, de->d_name);
		if(strstr(fpath,"/@ZA>AXio/") && de->d_type == DT_REG)
		{
			if(strcmp(decrypt, ".") != 0 && strcmp(decrypt, "..") != 0)
			{
				dec(decrypt);
				char npath[1000];
				sprintf(npath,"%s.iz1",decrypt);
				res = (filler(buf, npath, &st, 0));
				checkOwnerGroup(fullpath,npath);
			}
		}
		else
		{
            if(strcmp(decrypt, ".") != 0 && strcmp(decrypt, "..") != 0)
			{
				dec(decrypt);
				res = (filler(buf, decrypt, &st, 0));
				checkOwnerGroup(fullpath,decrypt);
				if(res!=0) break;
			}
		}
	}

	closedir(dp);
	return 0;
}

static int xmp_read(const char *path, char *st, size_t size, off_t offset,
		    struct fuse_file_info *fi)
{
  	char fpath[1000];
	if(strcmp(path,"/") == 0)
	{
		path=dirpath;
		sprintf(fpath,"%s",path);
	}
	else
	{	
		strcpy(encrypt, path);
		if(strcmp(encrypt, ".") != 0 && strcmp(encrypt, "..") != 0)
			enc(encrypt);

		sprintf(fpath,"%s%s", dirpath, encrypt);
	}
	int res = 0;
    int fd = 0 ;

	(void) fi;
	fd = open(fpath, O_RDONLY);
	if (fd == -1)
		return -errno;

	res = pread(fd, st, size, offset);
	if (res == -1)
		res = -errno;

	close(fd);
	return res;
}

static int xmp_mkdir(const char *path, mode_t mode)
{ 
    int res;
	char fpath[1000];
	char temp[1000];
	char youtuber[1000]="/YOUTUBER";

    strcpy(temp,path);
    enc(temp);
    sprintf(fpath, "%s%s", dirpath, temp);

    if(strstr(fpath,"/@ZA>AXio/"))
	{
		res = mkdir(fpath,0750);
	}
	else
	{
		res = mkdir(fpath,mode);
	} 
    
    if(res == -1) return -errno; 

    return 0; 
}

static int xmp_create(const char* path, mode_t mode, struct fuse_file_info* fi)
{
	int res, res2;
	char fpath[1000];
	char temp[1000];
	char file_baru[1000];
	char youtuber[1000]="/YOUTUBER";

    strcpy(temp,path);
    enc(temp);
    sprintf(fpath, "%s%s", dirpath, temp);

    if(strstr(fpath,"/@ZA>AXio/"))
	{
		res = creat(fpath,0640);
	}
	else
	{
		res = creat(fpath,mode);
	}

	close(res);

	if(strstr(fpath, "/@ZA>AXio/") != NULL)
    {
    	strcpy(file_baru, fpath);
    	strcat(file_baru, "`[S%");
    	res2 = rename(fpath, file_baru);
    	if(res2==-1)
        {
                return -errno;
        }
    }

    if(res == -1)
	return -errno;

    return 0;
}

static int xmp_chmod (const char *path, mode_t mode)
{
    int res;
    char fpath[1000];
	char cpath[1000];
	char youtuber[1000]="/YOUTUBER";
	sprintf(cpath, "%s", path);
	enc(cpath);
    sprintf(fpath, "%s%s", dirpath, cpath);

	if(strstr(fpath, "/@ZA>AXio/") != NULL)
    {	
    	size_t ext = strlen(fpath), exts = strlen("`[S%");
    	if(ext >= exts && !strcmp(fpath + ext - exts, "`[S%"))
    	{
    		pid_t anak;

            anak = fork();
            if(anak == 0)
            {
                char *argv[5] = {"zenity", "--error", "--title=Error", "--text=File ekstensi iz1 tidak boleh diubah permisionnya", NULL};
                        execv("/usr/bin/zenity", argv);

                        return -errno;
            }
            return 0;
    	}
    	else res = chmod(fpath, mode);
    }
    else res = chmod(fpath, mode);
    

    if(res==-1)
    {
            return -errno;
    }
    return 0;
}

static int xmp_chown(const char *path, uid_t uid, gid_t gid)
{
	int res;
	char fpath[1000];
	char cpath[1000];
	sprintf(cpath, "%s", path);
	sprintf(fpath,"%s%s", dirpath,path);
	res = lchown(fpath,uid,gid);
	if(res==-1)
	{
		return -errno;
	}
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
                                char tanggal[40];
                                time_t ltime;
                                struct tm *timeinfo;
                                time(&ltime);
                                timeinfo = localtime(&ltime);
                                strftime(tanggal, sizeof(tanggal), "%Y:%m:%d_d:%H:%M:%S", timeinfo); 
                                //FILE *fromfile = fopen(dir->d_name, "w+");
                                mkdir("Backup",0777);
                                //FILE *tofile = fopen("/home/test/Backup/dir->d_name", "w");
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

static struct fuse_operations xmp_oper = {
	.getattr	= xmp_getattr,
	.readdir	= xmp_readdir,
	.read		= xmp_read,
	.mkdir      = xmp_mkdir,
	.create     = xmp_create,
	.chmod      = xmp_chmod,
	.chown		= xmp_chown,
	.open       = xmp_open
};

int main(int argc, char *argv[])
{
	umask(0);
	return fuse_main(argc, argv, &xmp_oper, NULL);
}

void enc(char arr[])
{
	int n, i, ch;
	for(n = 0; arr[n] != '\0'; ++n)
	{
		ch = arr[n];

		if(ch == '/') continue;

		for(i = 0; i < strlen(caesar); i++)
		{
			if(ch == caesar[i]) break;
		}
		
		i = i + 17;
		if(i > 93) i = i - strlen(caesar);

		arr[n] = caesar[i];
	}
}

void dec(char arr[])
{
	int n, i, ch;
	for(n = 0; arr[n] != '\0'; ++n)
	{
		ch = arr[n];

		if(ch == '/') continue;

		for(i = 0; i < strlen(caesar); i++)
		{
			if(ch == caesar[i]) break;
		}
		
		i = i - 17;
		if(i < 0) i = i + strlen(caesar);

		arr[n] = caesar[i];
	}
}

void checkOwnerGroup(const char *path, const char *cpath)
{
	char copy[1000];
	sprintf(copy,"%s%s",dirpath,path);
	struct stat file;
    lstat(path,&file);
    struct group *grp;
    struct passwd *pwd;

    grp = getgrgid(file.st_uid);
    pwd = getpwuid(file.st_gid);

    if ((strcmp(grp->gr_name, "rusak")!=0 && ((strcmp(pwd->pw_name, "chipset")!=0 || strcmp(pwd->pw_name, "ic_controller")!=0))) && access(path,R_OK) !=0)
	{
		char filemiris[]="/filemiris.txt";
		char newfile[1000];
		sprintf(newfile,"%s%s",dirpath,filemiris);
        	FILE *out_file = fopen(newfile,"a");
        	fprintf(out_file,"%s %d %d %ld %s %s\n", cpath, file.st_uid, file.st_gid, file.st_atime, grp->gr_name, pwd->pw_name);
		fclose(out_file);
        	remove(path);
    }
}

```



# SoalShiftSISOP20_modul2_E11

## Kelompok
- Ammar Alifian Fahdan (05111840000007)
- Zaenal Mahmudi Ismail (05111840000054)

## Pembahasan Soal

### Soal 1

**Deskripsi**

Membuat program C yang menyerupai crontab untuk menjalankan script bash dengan ketentuan sebagai berikut:
**a.** Program menerima 4 argumen berupa:
    i. Detik: 0-59 atau * (any value)
    ii. Menit: 0-59 atau * (any value)
    iii. Jam: 0-23 atau * (any value)
    iv. Path file .sh
**b.** Program akan mengeluarkan pesan error jika argumen yang diberikan tidak sesuai
**c.** Program hanya menerima 1 config cron
**d.** Program berjalan di background (daemon)
**e.** Tidak boleh menggunakan fungsi system()

**Penjelasan Script**

Membuat daemon dari template
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

int main() {
  pid_t pid, sid;        // Variabel untuk menyimpan PID

  pid = fork();     // Menyimpan PID dari Child Process

  /* Keluar saat fork gagal
  * (nilai variabel pid < 0) */
  if (pid < 0) {
    exit(EXIT_FAILURE);
  }

  /* Keluar saat fork berhasil
  * (nilai variabel pid adalah PID dari child process) */
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

  while (1) {
    // Tulis program kalian di sini

    sleep(30);
  }
}
```
Bila program mendapat masalah di argumen atau di file .sh, keluarkan error. 

```
```
    if(argc<5){
        printf("Argumen kurang\n");
        return 0;
    }

    if((atoi(argv[1])>=0 &&atoi(argv[1])<60) || strcmp(argv[1],"*")==0){
        puts(argv[1]);
    }
    else{
        puts("Detik tidak valid.\n");
        return 0;
    }

    if((atoi(argv[2])>=0 &&atoi(argv[2])<60) || strcmp(argv[2],"*")==0){
        puts(argv[2]);
    }
    else{
        puts("Menit tidak valid.\n");
        return 0;
    }

    if((atoi(argv[3])>=0 &&atoi(argv[3])<60) || strcmp(argv[3],"*")==0){
        puts(argv[3]);
    }
    else{
        puts("Jam tidak valid.\n");
        return 0;
    }
    // puts(argv[4]);
    if(access(argv[4],F_OK)==-1){
        puts("File tidak ditemukan.\n");
        return 0;
    }
```
```
Setelah itu, gunakan fungsi ini untuk memparse waktu yang diberikan berdasarkan argumen.

```
```
    time_t rawtime=time(NULL);
    struct tm *timeinfo=localtime(&rawtime);
    int sec=timeinfo->tm_sec;
    int min=timeinfo->tm_min;
    int hour=timeinfo->tm_hour;

    if(sec==atoi(argv[1])||strcmp(argv[1],"*")==0){
        sec=100;
    }
    if(min==atoi(argv[2])||strcmp(argv[2],"*")==0){
        min=100;
    }
    if(hour==atoi(argv[3])||strcmp(argv[3],"*")==0){
        hour=100;
    }
    if(sec==100&&min==100&&hour==100){
        if(fork()==0){
            char *args[] = {"bash",argv[4],NULL};
            execv("/bin/bash",args);
        }   
    }
    sleep(1);
```
```


### Soal 3

**Deskripsi**

Jaya adalah seorang programmer handal mahasiswa informatika. Suatu hari dia memperoleh tugas yang banyak dan berbeda tetapi harus dikerjakan secara bersamaan (multiprocessing).
1. Program buatan jaya harus bisa membuat dua direktori di
“/home/[USER]/modul2/”. Direktori yang pertama diberi nama “indomie”, lalu
lima detik kemudian membuat direktori yang kedua bernama “sedaap”.
2. Kemudian program tersebut harus meng-ekstrak file jpg.zip di direktori “/home/[USER]/modul2/”. Setelah tugas sebelumnya selesai, ternyata tidak hanya itu tugasnya.
3. Diberilah tugas baru yaitu setelah di ekstrak, hasil dari ekstrakan tersebut (di dalam direktori “home/[USER]/modul2/jpg/”) harus dipindahkan sesuai dengan pengelompokan, semua file harus dipindahkan ke “/home/[USER]/modul2/sedaap/” dan semua direktori harus dipindahkan ke “/home/[USER]/modul2/indomie/”.
4. Untuk setiap direktori yang dipindahkan ke “/home/[USER]/modul2/indomie/” harus membuat dua file kosong. File yang pertama diberi nama “coba1.txt”, lalu 3 detik kemudian membuat file bernama “coba2.txt”. (contoh : “/home/[USER]/modul2/indomie/{nama_folder}/coba1.txt”).
Karena Jaya terlalu banyak tugas dia jadi stress, jadi bantulah Jaya agar bisa membuat
program tersebut.
Catatan :
- Tidak boleh memakai system().
- Tidak boleh memakai function C mkdir() ataupun rename().
- Gunakan exec dan fork
- Direktori “.” dan “..” tidak termasuk

**Penjelasan Script**
int main() dari program adalah sebagai berikut. 
```
int status_f;
	pid_t child_f = fork();

	if (child_f < 0) {
		exit(EXIT_FAILURE);
	}

	if (child_f == 0){
		int status;
		pid_t child_id = fork();

		if (child_id < 0) {
			exit(EXIT_FAILURE);
		}

		if (child_id == 0) {
			pid_t child_sedaap = fork();

			if (child_sedaap < 0) {
				exit(EXIT_FAILURE);
			}

			if (child_sedaap == 0){
				char* arg[] = {"mkdir", "/home/kerupuksambel/modul2/indomie", NULL};
				execv("/bin/mkdir", arg);
			}else{
				sleep(5);
				char* arg2[] = {"mkdir", "/home/kerupuksambel/modul2/sedaap", NULL};
				execv("/bin/mkdir", arg2);
			}
```
Pertama program membuat fork, lalu dalam fork akan memanggil fork lagi dan memanggil fork sekali lagi, dengan total 3 kali forking. Setelah itu, untuk child process, program akan memanggil mkdir untuk membuat folder indomie. Lalu di parent, program melakukan sleep selama 5 detik dan memanggil mkdir lagi untuk membuat sedaap.

```
		} else {
			while ((wait(&status)) > 0);
			char* arg[] = {"unzip", "/home/kerupuksambel/modul2/jpg.zip", "-d", "/home/kerupuksambel/modul2/", NULL};
			execv("/usr/bin/unzip", arg);
		}
```

Setelah itu, di else fork yang kedua (parent), program akan memanggil unzip untuk unzip jpg.zip di folder modul2.

```
	}else{
		while ((wait(&status_f)) > 0);
		pid_t child_id_2 = fork();
		int status_2;

		if (child_id_2 < 0) {
			exit(EXIT_FAILURE);
		}

		if (child_id_2 == 0){
			DIR *dp;
			struct dirent *ep;     
			dp = opendir ("/home/kerupuksambel/modul2/jpg/");

			if (dp != NULL){
				while (ep = readdir (dp)){
					pindah(ep->d_name, ep->d_type);
				}
				(void) closedir (dp);
			}
		}else{
			while ((wait(&status_2)) > 0);
			printf("done\n");
			DIR *dp2;
			struct dirent *ep2;     
			dp2 = opendir ("/home/kerupuksambel/modul2/indomie/");

			if (dp2 != NULL){
				while (ep2 = readdir (dp2)){
					buat(ep2->d_name);
				}
				(void) closedir (dp2);
			}
		}
	}
}
```

Lalu, di else fork yang pertama, buat satu fork lagi. Child process untuk melakukan scanning directory dan memanggil fungsi `pindah()` untuk setiap file, dan parent process untuk memanggil fungsi `buat()` di setiap folder di `indomie`.

```
void pindah(char* lokasi, int flag){
	char lokasi_new[] = "/home/kerupuksambel/modul2/jpg/";
	strcat(lokasi_new, lokasi);
	if(_check_existence(lokasi_new)){
		pid_t child2 = fork();
		int flag_do = 0, s;
		if(child2 == 0){
			if(flag == 4){
				//merupakan direktori
				if(strcmp(lokasi, ".") != 0 && strcmp(lokasi, "..") != 0){
					strcat(lokasi_new, "/");
					char* arg[] = {"mv", lokasi_new, "/home/kerupuksambel/modul2/indomie", NULL};
					execv("/bin/mv", arg);
					// pid_t child3 = fork();
				}
			}else{
				//merupakan file
				char* arg[] = {"mv", lokasi_new, "/home/kerupuksambel/modul2/sedaap", NULL};
				execv("/bin/mv", arg);
			}
		}else{
			while ((wait(&s)) > 0);
			if(!_check_existence(lokasi_new)){
				printf("File sudah dipindah.\n");
			}else{

			}
		}
	}else{
		exit(EXIT_SUCCESS);
	}
}
```

Untuk fungsi pindah(), pertama program mengecek keberadaan file lewat fungsi `_check_existence()`, bila file masih ada, program membuat fork, dengan child process mengecek apakah format dari file adalah file atau direktori dengan mengecek flag yang berasal dari tipe file, lalu melakukan pemindahan ke tempat yang benar. Lalu untuk parent, program akan mengecek lagi apakah file sudah dipindah. Namun bila file sudah tidak ada saat pengecekan awal, maka program akan exit.

```
int _check_existence(char* lokasi){
	if(access(lokasi, F_OK) != -1){
		return 1;
	}else{
		return 0;
	}
}
```
Fungsi `_check_existence` akan memanggil access untuk lokasi file, bila file ada, maka return 1, bila file tidak ditemukan, maka return 0.

```
void buat(char* lokasi){
	// char lokasi_new[] = "/home/kerupuksambel/modul2/indomie/";
	// strcat(lokasi_new, lokasi);
	if(strcmp(lokasi, ".") != 0 && strcmp(lokasi, "..") != 0){
		pid_t before_make = fork();
		int st2;
		if(before_make == 0){
			pid_t child_make = fork();
			int st;

			if(child_make == 0){
				char nama_1[] = "/home/kerupuksambel/modul2/indomie/";
				strcat(nama_1, lokasi);
				strcat(nama_1, "/coba1.txt");
				char* arg[] = {"touch", nama_1, NULL};
				execv("/usr/bin/touch", arg);
			}else{
				while ((wait(&st)) > 0);
				sleep(3);
				char nama_2[] = "/home/kerupuksambel/modul2/indomie/";
				strcat(nama_2, lokasi);
				strcat(nama_2, "/coba2.txt");
				char* arg2[] = {"touch", nama_2, NULL};
				execv("/usr/bin/touch", arg2);
			}
		}else{
			while ((wait(&st2)) > 0);
			printf("File coba1 dan coba2 terbentuk\n");
			// exit(EXIT_SUCCESS);
		}
	}else{
	}
}
```

Untuk fungsi buat(), program akan membuat fork, di child process program akan membuat fork lagi. Di child dari fork kedua, program akan membuat coba1.txt di lokasi dengan perintah touch, dan di parent dari fork kedua, program akan membuat coba2.txt.  Di parent fork pertama, program akan mengeluarkan pesan bila file berhasil terbentuk.

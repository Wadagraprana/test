[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-24ddc0f5d75046c5622901739e7c5dd533143b0c8e959d652212380cedb1ea36.svg)](https://classroom.github.com/a/jYae_yK9)
<div align=center>

|    NRP     |            Name            |
| :--------: | :------------------------: |
| 5025221101 |   Lalu Aldo Wadagraprana   |
| 5025221130 | Lathiifah Nabiila Bakhtiar |
| 5025221275 |    Muhammad Alfan Mahdi    |

# Praktikum Modul 3 _(Module 3 Lab Work)_

</div>

## Daftar Soal _(Task List)_

- [Task 1 - Jess No Limit Push Rank](/task-1/)

- [Task 2 - BNBWT](/task-2/)

- [Task 3 - Volume Expansion](/task-3/)

- [Task 4 - WOTA Competition](/task-4/)

## Laporan Resmi Praktikum Modul 3 _(Module 3 Lab Work Report)_

### Task 1
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/stat.h>
#include <string.h>
#include <sys/wait.h>

struct atributhero {
    char Nama[20];
    int HP;
    float HPRegen;
    int PhysicalAtk;
    int PhysicalDfs;
    float AtkSpeed;
    int AtkSpeed_pc;
    int Mana;
    int ManaRegen;
    int MagicPwr;
    int MagicDfs;
    int MvmntSpeed;
    int CritChance;
    int CritDmg;
    int CritDmgRed;
    int CdRed;
    int SpellVamp;
    int MagicLifesteal;
    int PhysicalPEN;
    int PhysicalPEN_Persen;
    int MagicalPEN;
    int MagicalPEN_Persen;
    int Resilience;
    int EfekPemulihan;
    int PemulihanDiterima;
    float AtkSpeedTotal;
    int Skillpoint;
};

struct datahero {
    char Nama[20];
    char Role[20];
    char Tipe[50];
    int TahunRilis;
};

void parseCSV(char *line, char *values[], int max_values, char delimiter) {
    int i = 0;
    char *token = strtok(line, &delimiter);
    while (token != NULL && i < max_values) {
        values[i++] = token;
        token = strtok(NULL, &delimiter);
    }
}

int heroterbaik(struct datahero hero[], int jumlahhero, struct atributhero atribut[], const char *Role) {
    int index = -1;
    int skillpointterbesar = 0;
    for (int i = 0; i < jumlahhero; i++) {
        char *heroRoles = strdup(hero[i].Role); 
        char *token = strtok(heroRoles, "/");
        while (token != NULL) {
             if (strcmp(token, Role) == 0) {
                int Skillpoint = 0;
                if (strcmp(Role, "TANK") == 0) Skillpoint = atribut[i].HP;
                else if (strcmp(Role, "ASSASSIN") == 0) Skillpoint = atribut[i].PhysicalAtk;
                else if (strcmp(Role, "FIGHTER") == 0) Skillpoint = atribut[i].HP + atribut[i].PhysicalAtk;
                else if (strcmp(Role, "MM") == 0) Skillpoint = (int)(atribut[i].PhysicalAtk * atribut[i].AtkSpeed);
                else if (strcmp(Role, "MAGE") == 0) {
                    if (atribut[i].ManaRegen != 0) Skillpoint = atribut[i].Mana / atribut[i].ManaRegen;
                } else if (strcmp(Role, "SUPPORT") == 0) Skillpoint = atribut[i].ManaRegen + atribut[i].HP;
                if (Skillpoint > skillpointterbesar) {
                    skillpointterbesar = Skillpoint;
                    index = i;
                }
            } token = strtok(NULL, ",");
        } free(heroRoles); 
    } if (index != -1) atribut[index].Skillpoint = skillpointterbesar;
    return index;
}

int main(int argc, char *argv[]) {
    struct stat st = {0};
    if (stat("Data Mobile Legend", &st) == -1) {
        system("kaggle datasets download -d irwansyah10010/mobilelegend");

        pid_t pid = fork();
        if (pid == 0) {
            execlp("unzip", "unzip", "mobilelegend.zip", NULL);
            exit(1);
        } else if (pid > 0) {
            waitpid(pid, NULL, 0);
        } else {
            perror("Error forking");
            exit(1);
        }
    }

    FILE *fileatribut = fopen("Data Mobile Legend/Atribut/atribut-hero.csv", "r");
    if (!fileatribut) {
        fprintf(stderr, "Error opening file atribut.\n");
        return 1;
    }

    struct atributhero atribut;
    char baris[1024];
    char *kolom[26];
    int indeksatribut = 0;
    struct atributhero attribute[150];
    while (fgets(baris, sizeof(baris), fileatribut)) {
        parseCSV(baris, kolom, 26, ';');
        strcpy(attribute[indeksatribut].Nama, kolom[0]);
        attribute[indeksatribut].HP = atoi(kolom[1]);
        attribute[indeksatribut].HPRegen = atof(kolom[2]);
        attribute[indeksatribut].PhysicalAtk = atoi(kolom[3]);
        attribute[indeksatribut].PhysicalDfs = atoi(kolom[4]);
        attribute[indeksatribut].AtkSpeed = atof(kolom[5]);
        attribute[indeksatribut].AtkSpeed_pc = atoi(kolom[6]);
        attribute[indeksatribut].Mana = atoi(kolom[7]);
        attribute[indeksatribut].ManaRegen = atoi(kolom[8]);
        attribute[indeksatribut].MagicPwr = atoi(kolom[9]);
        attribute[indeksatribut].MagicDfs = atoi(kolom[10]);
        attribute[indeksatribut].MvmntSpeed = atoi(kolom[11]);
        attribute[indeksatribut].CritChance = atoi(kolom[12]);
        attribute[indeksatribut].CritDmg = atoi(kolom[13]);
        attribute[indeksatribut].CritDmgRed = atoi(kolom[14]);
        attribute[indeksatribut].CdRed = atoi(kolom[15]);
        attribute[indeksatribut].SpellVamp = atoi(kolom[16]);
        attribute[indeksatribut].MagicLifesteal = atoi(kolom[17]);
        attribute[indeksatribut].PhysicalPEN = atoi(kolom[18]);
        attribute[indeksatribut].PhysicalPEN_Persen = atoi(kolom[19]);
        attribute[indeksatribut].MagicalPEN = atoi(kolom[20]);
        attribute[indeksatribut].MagicalPEN_Persen = atoi(kolom[21]);
        attribute[indeksatribut].Resilience = atoi(kolom[22]);
        attribute[indeksatribut].EfekPemulihan = atoi(kolom[23]);
        attribute[indeksatribut].PemulihanDiterima = atoi(kolom[24]);
        attribute[indeksatribut].AtkSpeedTotal = atof(kolom[25]);
        attribute[indeksatribut].Skillpoint = 0;
        indeksatribut++;
    } fclose(fileatribut);

    FILE *fileidentitas = fopen("Data Mobile Legend/identitas.csv", "r");
    if (!fileidentitas) {
        fprintf(stderr, "Error opening file identitas.\n");
        return 1;
    }

    struct datahero hero;
    struct datahero heroes[150];
    int indekshero = 0;
    while (fgets(baris, sizeof(baris), fileidentitas)) {
        parseCSV(baris, kolom, 4, ';');
        strcpy(heroes[indekshero].Nama, kolom[0]);
        strcpy(heroes[indekshero].Role, kolom[1]);
        strcpy(heroes[indekshero].Tipe, kolom[2]);
        heroes[indekshero].TahunRilis = atoi(kolom[3]);
        indekshero++;
    } fclose(fileidentitas);

    const char *Role;
    if (argc == 2) {
        Role = argv[1]; 
        int index = heroterbaik(heroes, indekshero, attribute, Role);
        if (index != -1) printf("Hero terbaik adalah %s dengan skillpoint %d.\n", heroes[index].Nama, attribute[index].Skillpoint);
        else printf("No hero found for Role %s.\n", Role);
    } else {
        const char *Roles[] = {"TANK", "ASSASSIN", "FIGHTER", "MM", "MAGE", "SUPPORT"};
        for (int i = 0; i < sizeof(Roles) / sizeof(Roles[0]); i++) {
            int index = heroterbaik(heroes, indekshero, attribute, Roles[i]);
            if (index != -1) printf("Hero terbaik adalah %s dengan skillpoint %d.\n", heroes[index].Nama, attribute[index].Skillpoint);
            else printf("No hero found for Role %s.\n", Roles[i]);
        }
    }
    return 0;
}
```

### Cara Pengerjaan
#### Bagian a
```c
struct stat st = {0};
    if (stat("Data Mobile Legend", &st) == -1) {
        system("kaggle datasets download -d irwansyah10010/mobilelegend");

        pid_t pid = fork();
        if (pid == 0) {
            execlp("unzip", "unzip", "mobilelegend.zip", NULL);
            exit(1);
        } else if (pid > 0) {
            waitpid(pid, NULL, 0);
        } else {
            perror("Error forking");
            exit(1);
        }
    }
```
- Program akan mengecek apakah sudah ada folder `Data Mobile Legend`. Jika belum, akan mendownload file `mobilelegend.zip` dengan `system("kaggle datasets download -d irwansyah10010/mobilelegend");`
- Setelah itu file `mobilelegend.zip` diextract
  
#### Bagian b
- Pertama, definisikan struct `atributhero` yang akan digunakan untuk menyimpan data dari file `atribut-hero.csv`.
- Lalu, definisikan struct `datahero` yang akan digunakan untuk menyimpan data dari file `identitas.csv`.
- Buat fungsi `parseCSV` untuk memecah baris CSV menjadi array string yang berisi nilai-nilai dari kolom-kolom yang dipisahkan oleh delimiter tertentu.
- Juga buat fungsi `heroterbaik ` untuk mencari hero terbaik dari setiap role.
  - Fungsi tersebut melakukan iterasi sebanyak jumlah hero.
  - `char *heroRoles = strdup(hero[i].Role)` Mengalokasikan memori dan menyalin string Role hero ke variabel heroRoles.
  -  `char *token = strtok(heroRoles, "/")` Memecah heroRoles berdasarkan delimiter `/` dan mendapatkan token pertama.
  -  Dilakukan iterasi selama token tidak kosong untuk menghitung skillpoint setiap hero.
  -  `if (strcmp(token, Role) == 0)` Membandingkan token sama role yang dicari. Jika sama, role hero tersebut sudah sesuai.
  -  Kemudidan, `if (strcmp(Role, "TANK") == 0) Skillpoint = atribut[i].HP` memeriksa apakah role hero adalah `tank` lalu menghitung `skillpoint` hero sesuai dengan rumus skillpoint pada soal. Dibuat juga pengkondisian untuk roles lain.
  -  ```c
     if (Skillpoint > skillpointterbesar) {
                    skillpointterbesar = Skillpoint;
                    index = i;
                }
     ```
     Jika `skillpoint` yang baru dihitung lebih besar dari `skillpointterbesar` sebelumnya, `skillpointterbesar` diupdate dengan `skillpoint` dan `index` diupdate dengan `i`.
  - `token = strtok(NULL, ",")` Mengambil `token` berikutnya dari string `heroRoles`.
  - Setelah iterasi selesai, `free(heroRoles)` Mendealokasikan memori yang telah dialokasikan sebelumnya untuk `heroRoles`.
  - `if (index != -1) atribut[index].Skillpoint = skillpointterbesar` Jika terdapat hero yang sesuai dengan role yg dicari, nilai `skillpoint` terbesar dari hero tersebut disimpan ke dalam array `atribut` pada index yang sesuai.
- Pada main, `FILE *fileatribut = fopen("Data Mobile Legend/Atribut/atribut-hero.csv", "r")` membuka file `atribut-hero.csv` dalam mode baca atau `r`.
- Setelah itu, `while (fgets(baris, sizeof(baris), fileatribut))` iterasi untuk membaca setiap baris dari file CSV `fileatribut` dan menyimpannya ke dalam array `baris`.
- fungsi `parseCSV(baris, kolom, 26, ';')` dipanggil untuk memisahkan baris yang telah dibaca menjadi beberapa kolom berdasarkan delimiter `;`. Setiap kolom akan disimpan dalam array `kolom`.
- `strcpy(attribute[indeksatribut].Nama, kolom[0])` untuk menyalin nilai dari kolom pertama ke dalam atribut `Nama` dari `attribute[indeksatribut]`.
- `attribute[indeksatribut].HP = atoi(kolom[1])` untuk mengubah nilai dari kolom kedua menjadi bilangan bulat menggunakan fungsi `atoi` dan menyimpannya ke dalam atribut `HP` dari `attribute[indeksatribut]`.
- Dilakukan hal yang sama untuk kolom lainnya dan `indeksatribut++`.
- Setelah iterasi selesai, `fclose(fileatribut)` menutup file `atribut-hero.csv`.
- Hal serupa juga dilakukan untuk file `identitas.csv`.
  ```c
    struct datahero heroes[150];
    int indekshero = 0;
    while (fgets(baris, sizeof(baris), fileidentitas)) {
        parseCSV(baris, kolom, 4, ';');
        strcpy(heroes[indekshero].Nama, kolom[0]);
        strcpy(heroes[indekshero].Role, kolom[1]);
        strcpy(heroes[indekshero].Tipe, kolom[2]);
        heroes[indekshero].TahunRilis = atoi(kolom[3]);
        indekshero++;
    } fclose(fileidentitas);
  ```
- Selanjutnya, `if (argc == 2)` memeriksa apakah jumlah argumen saat menjalankan program ada 2. Jika iya, `Role = argv[1]` argumennya disimpan ke dalam variabel `role`. `int index = heroterbaik(heroes, indekshero, attribute, Role)` memanggil fungsi `heroterbaik` untuk mencari hero terbaik berdasarkan role yang diberikan dan menyimpan indeks hero terbaik ke dalam variabel `index`. `if (index != -1) printf("Hero terbaik adalah %s dengan skillpoint %d.\n", heroes[index].Nama, attribute[index].Skillpoint)` Jika hero terbaik ditemukan, akan mencetak nama hero terbaik beserta skillpointnya.
- Jika tidak, `const char *Roles[] = {"TANK", "ASSASSIN", "FIGHTER", "MM", "MAGE", "SUPPORT"}` mendefinisikan array `Roles` yang berisi nama roles dari hero. `for (int i = 0; i < sizeof(Roles) / sizeof(Roles[0]); i++)` iterasi untuk setiap role dan akan menjalankan `int index = heroterbaik(heroes, indekshero, attribute, Roles[i])` serta `if (index != -1) printf("Hero terbaik adalah %s dengan skillpoint %d.\n", heroes[index].Nama, attribute[index].Skillpoint)`.
  
#### Bagian c
```
# Use the official Python image as the base image
FROM python:3.8-slim

# Set the working directory in the container
WORKDIR /app

# Install the unzip package
RUN apt-get update && apt-get install -y unzip

# Install the build-essential package to get gcc compiler
RUN apt-get install -y build-essential

# Install the Kaggle API client
RUN pip install kaggle

# Copy the Kaggle configuration file into the container
COPY kaggle.json /root/.kaggle/kaggle.json

# Set permissions for the Kaggle configuration file
RUN chmod 600 /root/.kaggle/kaggle.json

# Download the dataset
RUN kaggle datasets download -d irwansyah10010/mobilelegend

# Unzip the dataset
RUN unzip mobilelegend.zip

# Copy your C source file into the container
COPY ayamgoreng.c /app

# Compile the C program
RUN gcc ayamgoreng.c -o ayamgoreng

# Run the C program
CMD ["./ayamgoreng"]
```
Membuat dockerfile dengan 
- `python:3.8-slim` sebagai base image.
- `WORKDIR /app` menetapkan direktori kerja di dalam kontainer sebagai '/app' di mana semua perintah selanjutnya akan dijalankan di direktori ini.
- `RUN apt-get update && apt-get install -y unzip` memperbarui daftar paket dan menginstal paket `unzip` untuk mengekstrak file.
- `RUN apt-get install -y build-essential` menginstal paket `build-essential` yang berisi alat-alat yang diperlukan untuk mengompilasi program C, termasuk `compiler GCC`.
- `RUN pip install kaggle` menginstal paket `kaggle`
- `COPY kaggle.json /root/.kaggle/kaggle.json` menyalin file konfigurasi `kaggle.json` dari sistem host ke dalam kontainer di direktori `/root/.kaggle/`
- `RUN chmod 600 /root/.kaggle/kaggle.json` mengubah izin file agar hanya dapat diakses oleh pemiliknya.
- `RUN kaggle datasets download -d irwansyah10010/mobilelegend` mengunduh dataset dari `Kaggle`
- `RUN unzip mobilelegend.zip` mengekstrak file dataset yang telah diunduh.
- `COPY ayamgoreng.c /app` menyalin file `ayamgoreng.c` dari sistem host ke dalam kontainer di direktori `/app`.
- `RUN gcc ayamgoreng.c -o ayamgoreng` mengompilasi program C menjadi file biner yang dapat dijalankan dengan nama `ayamgoreng`.
- `CMD ["./ayamgoreng"]` menjalankan program `ayamgoreng` ketika kontainer dimulai.

#### Bagian d
- Push dockerfilenya ke `Docker Hub` dengan
  ```c
  docker login
  docker tag ayamgoreng-app lnabiila/joki-ml-bang
  docker push lnabiila/joki-ml-bang
  ```
- Hasilnya dapat dilihat melalui link `https://hub.docker.com/r/lnabiila/joki-ml-bang`

#### Bagian e
Membuat `Docker Compose`
  ```
  version: '3'
  services:
  tank:
    build: .
    command: ["./ayamgoreng", "TANK"]
  assassin:
    build: .
    command: ["./ayamgoreng", "ASSASSIN"]
  fighter:
    build: .
    command: ["./ayamgoreng", "FIGHTER"]
  marksman:
    build: .
    command: ["./ayamgoreng", "MM"]
  mage:
    build: .
    command: ["./ayamgoreng", "MAGE"]
  support:
    build: .
    command: ["./ayamgoreng", "SUPPORT"]
  ```
dengan mendefinisikan services yang akan dibangun dari `Dockerfile` yang telah dibuat. Setiap service akan menjalankan program `ayamgoreng` dengan argumen yang berbeda sesuai dengan role.

### _Screenshot_ Hasil
![image](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/117833447/1bee3dde-bb73-48d1-9f30-ed701cc20886)
![image](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/117833447/fe1ed0d4-f8cd-475b-8546-b04460bd0b77)
![image](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/117833447/cd02115b-e2cb-41be-af33-58ffd7124b23)
![image](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/117833447/b96d6b33-480c-4688-bf47-b90016e9e073)

### Task 2

### Penjelasan Kode

### Task-2 a
a. Buatlah 2 file, yaitu server.c dan client.c.

Disaat server.c pertama kali dijalankan, dia akan meminta prompt user untuk mengetikkan berapa banyak client yang akan dilayani (range 1-5).
sehingga bisa dimplementasikan pada server.c menjadi:
```c
//variable global
#define MAX_CLIENTS 5

int main() {
    // ...
    int num_clients;
    printf("Enter the number of clients to serve (1-5): ");
    scanf("%d", &num_clients);

    // Limit the number of clients to be served to MAX_CLIENTS
    num_clients = (num_clients > MAX_CLIENTS) ? MAX_CLIENTS : num_clients;
    // ...
}
```
dari subtask a bagian serer.c ini memiliki logika:
- melakukan inisiasi/deklarasi `int num_client;` untuk nantinya menjadi flag atau toggle untuk for loop.
- selanjutnya adalah melakukan `printf("Enter the number of clients to serve (1-5): ");` sesuai dengan perintah soal.
- dan terakhir adalah melakukan `scanf("%d", &num_clients);` untuk mengambil iput dari user berapa banyak flag atau toggle yang dinput, dengan keterangan bahwa angka `int` yang diinput dimulai dari angka 1 sampai 5.
- untuk mengecek apakah input dari user ini benar angka int 1 sampai 5 adalah dengan melakukan cek menggunakan `num_clients = (num_clients > MAX_CLIENTS) ? MAX_CLIENTS : num_clients;` dimana `MAX_CLIENTS` merupakan variable global yang di deklarasikan di awal.

Disaat client.c pertama kali dijalankan, dia akan meminta prompt user untuk mengetikkan username. Username ini kemudian dikirim ke server, untuk di check apakah sudah ada sebelumnya atau tidak. Jika sudah ada, maka akan meminta prompt lagi untuk mengetikkan username sampai diterima oleh server.
sehingga bisa dimplementasikan pada client.c menjadi:
```c
//variable global
#define MAX_USERNAME_LENGTH 50

int main(int argc, char *argv[])
{
    char username[MAX_USERNAME_LENGTH];
    //...
    int client_index = atoi(argv[1]);
    //...
    while (check_username)
    {
        // Prompt user to enter username
        printf("Enter your username for client %d: ", client_index);
        fgets(username, MAX_USERNAME_LENGTH, stdin);
        strtok(username, "\n"); // Remove newline character from username
    //...
    }
    //...
}
```
dari subtask a bagian client.c ini memiliki logika:
- melakukan inisiasi/deklarasi `char username[MAX_USERNAME_LENGTH];` untuk nantinya menjadi char penyimpan username, dan `int client_index = atoi(argv[1]);`utuk nantinya menjadi flag atau toggle bahwa client ini adalah client ke berapa.
- memaski fungsi `while (check_username)` untuk nantiinya memlakukan perintah selanjutnya.
- melakukan printf `printf("Enter your username for client %d: ", client_index);` sesuai perintah soal.
- melakukan perintah `fgets(username, MAX_USERNAME_LENGTH, stdin);` untuk mengabil `char username` yang memiliki persyaratan sesuai dengan define variable global `MAX_USERNAME_LENGTH` dan melakukan `stdin` untuk standar input.
- melakukan perintah `strtok(username, "\n");` untuk memisah antar username yang nantinya akan di input oleh client dengan menggunakan delimiter \n (atau setiap username yang diinput akan bagi berdasakran enter).

### Task-2 b
b. Ketika semua client yang dibutuhkan oleh server telah terkumpul, setiap client akan mulai diberikan prompt untuk mengetikkan service yang dibutuhkan, yaitu Nama Container, Image yang Digunakan, Perintah Saat Kontainer Dimulai, dan Volume. Urutan client yang mengirimkan request service ke server ditentukan dari waktu mereka register. Contoh: Jika username ragnar registrasi pertama kali, lalu diikuti username towel, maka ragnar yang akan mengirim request terlebih dahulu, lalu diikuti towel, dst.

Yang dijalankan pada server.c
```c
// Function to handle username request
void handle_username(struct msg_buffer *request, struct msg_buffer *response)
{
    // Check if username already exists
    bool username_exists = false;
    for (int i = 0; i < num_used_usernames; i++)
    {
        if (strcmp(request->username, used_usernames_list[i].username) == 0)
        {
            username_exists = true;
            break;
        }
    }

    if (username_exists)
    {
        // Username already exists, send failure response
        response->success = false;
    }
    else
    {
        // Add username to used usernames list
        strcpy(used_usernames_list[num_used_usernames].username, request->username);
        num_used_usernames++;

        // Username is accepted, send success response
        response->success = true;
    }
}

// Function to handle docker_info request
void handle_docker_info(struct msg_buffer *request, struct msg_buffer *response)
{
    // Find the index of the username if it exists
    int username_index = -1;
    for (int i = 0; i < num_used_usernames; i++)
    {
        if (strcmp(request->username, used_usernames_list[i].username) == 0)
        {
            username_index = i;
            break;
        }
    }
    //...
}
//kode main akan dipanggil:
int main()
{
    //...
    // Accept clients and process requests
    while (1)
    {
        //...
        else
        {
            //...
            // Process request based on request type
            if (strcmp(request.request_type, "username") == 0)
            {
                handle_username(&request, &response);
            }
            else if (strcmp(request.request_type, "docker_info") == 0)
            {
                handle_docker_info(&request, &response);
            }
            //...
        }
    }
    //...
}

```
dari subtask b bagian server.c ini memiliki logika:
- pada bagian `server.c` ini kodingan akan melakukan pengecekan pada username da akan melanjutkan task apabila semua client sudah melakukan input username, dan nantinya akan bisa melakukan perintah selanjutnya yaitu melakukan input `docker_info` pada `client.c`.
- langkah pertama yaitu melakukan pengecekan username dengan fungsi `void handle_username(struct msg_buffer *request, struct msg_buffer *response)` dengan logika didalamnya yaitu:
  + melakukakm deklarasi `bool username_exists = false;`.
  + melakukan pengecekan apakah `username` exists dengan for loop `for (int i = 0; i < num_used_usernames; i++)` yang memiliki logika:
    * melakukan if else, yaitu `if (strcmp(request->username, used_usernames_list[i].username) == 0)` untk mengecek apakah username yang direquest dali `client.c` sudah pernah diinput sebelumnya.
    * jika memenuhi `username_exists = true;` maka akan melakukan `break;`
  + selanjutnya adalah mengirim pemberitahuan pada `client.c` jika username memang sudah ada dengan if else pertama `if (username_exists)` yang berisi `response->success = false;`.
  + melakukan fungsi if else kedua yaitu `else` yang artinya username tidak sama, dengan logika:
    * melakukan perintah `strcpy(used_usernames_list[num_used_usernames].username, request->username);` untuk menyalin `username`.
    * melakukan count dengan `num_used_usernames++;`
    * dan akan mengirim pemberitahuan pada `client.c` bahwa username telah sukses diinput dengan `response->success = true;`.
- langkah selanjutnya adalah mengecek apakah semua client telah siap seperti pada soal dengan salah satu bagian dalam fungsi `void handle_docker_info(struct msg_buffer *request, struct msg_buffer *response)` yang berisi logika:
  + melakukan deklarasi `int username_index = -1;`.
  + melakukan for loop untuk mengecek index username denga `for (int i = 0; i < num_used_usernames; i++)`:
    * melakukan if else `if (strcmp(request->username, used_usernames_list[i].username) == 0)`
    * jika memenuhi `username_index = i;` maka akan melakukan `break;`
- langkah berikutnuya semua fungsi diatas akan dijalankan di `int main()` pada loop `while` yang didalamnya terdapat logika:
  + pada bagian if else `else` yang memiliki logika didalamnya:
    * menjalankan fungsi if else pertama, yaitu `if (strcmp(request.request_type, "username") == 0)` yang didalamnya akan memanggil fungsi `handle_username(&request, &response);` yang telah dibuat sebelumnya.
    * menjalankan fungsi if else kedua, yaitu `else if (strcmp(request.request_type, "docker_info") == 0)` yang didalamnya akan memanggil fungsi `handle_docker_info(&request, &response);` yang telah dibuat sebelumnya.

Yang dijalankan pada client.c
```c
//...
void send_docker_info_request(int msgid, const char *username, struct msg_buffer *request, struct msg_buffer *response)
{
    // Prompt user to enter docker info
    printf("Enter Docker Info for %s:\n", username);

    // Nama Container
    printf("Nama Container: ");
    fgets(request->nama_container, sizeof(request->nama_container), stdin);
    request->nama_container[strcspn(request->nama_container, "\n")] = 0; // Remove newline character

    // Image
    printf("Image: ");
    fgets(request->image, sizeof(request->image), stdin);
    request->image[strcspn(request->image, "\n")] = 0; // Remove newline character

    // Perintah Saat Kontainer Dimulai
    printf("Perintah Saat Kontainer Dimulai: ");
    fgets(request->perintah, sizeof(request->perintah), stdin);
    request->perintah[strcspn(request->perintah, "\n")] = 0; // Remove newline character

    // Volume
    printf("Volume: ");
    fgets(request->volume, sizeof(request->volume), stdin);
    request->volume[strcspn(request->volume, "\n")] = 0; // Remove newline character

    // Send docker info request to server
    request->msg_type = MSG_TYPE;
    strcpy(request->request_type, "docker_info");
    strcpy(request->username, username);
    if (msgsnd(msgid, request, sizeof(struct msg_buffer), 0) == -1)
    {
        perror("msgsnd failed");
        exit(EXIT_FAILURE);
    }

    // Wait for server response
    if (msgrcv(msgid, response, sizeof(struct msg_buffer), MSG_TYPE, 0) == -1)
    {
        perror("msgrcv failed");
        exit(EXIT_FAILURE);
    }

    // Check if server accepted docker info request
    if (response->success)
    {
        printf("Berhasil memasukkan data docker untuk %s\n", username);
    }
    else
    {
        printf("Gagal memasukkan data docker untuk %s\n", username);
    }
    clear_msg_buffer(request);
    clear_msg_buffer(response);
}
```
```c
int main(int argc, char *argv[])
{
    //...
    // Main loop to send requests to server
    while (1)
    {
        // Prompt user to enter service name
        char service_name[20];
        printf("Enter service name (or 'exit' to quit): ");
        fgets(service_name, sizeof(service_name), stdin);
        service_name[strcspn(service_name, "\n")] = 0; // Remove newline character

        // Check if user wants to exit
        if (strcmp(service_name, "exit") == 0)
        {
            exit_request(msgid, &request, &response);
            break; // Exit loop and terminate client
        }
        // Check if user wants to enter docker info
        else if (strcmp(service_name, "docker_info") == 0)
        {
            send_docker_info_request(msgid, username, &request, &response);
        }
        else
        {
            // Process other service requests here...

            // Dummy implementation: just print service name
            printf("Requested service: %s (Client %d)\n", service_name, client_index);
        }
    }
    //...
}
```
dari subtask b bagian client.c ini memiliki logika:
- pada bagian `void send_docker_info_request(int msgid, const char *username, struct msg_buffer *request, struct msg_buffer *response)` adalah untuk menjalankan perintah sesuai soal subtask b, adapaun logikanya yaitu:
  + pertama akan melakukan printf `printf("Enter Docker Info for %s:\n", username);` untuk pemberitahuan bahwa user tersebut telah memilih fungsi `docker_info`.
  + akan mulai melakukan input dari user untuk `Nama Container` dari soal dengan logika:
    * melakukan printf `printf("Nama Container: ");` untuk memberi tahu user melakukan input.
    * melakukan perintah `fgets(request->nama_container, sizeof(request->nama_container), stdin);` unuk mengambil `char` dari `nama_container`.
    * melakukan perintah `request->nama_container[strcspn(request->nama_container, "\n")] = 0;` untuk mengubah karakter di indeks tersebut menjadi null terminator (\0) setelah ada `\n`.
  + akan mulai melakukan input dari user untuk `Image yang Digunakan` dari soal dengan logika:
    * melakukan printf `printf("Image: ");` untuk memberi tahu user melakukan input.
    * melakukan perintah `fgets(request->image, sizeof(request->image), stdin);` unuk mengambil `char` dari `image`.
    * melakukan perintah `request->image[strcspn(request->image, "\n")] = 0;` untuk mengubah karakter di indeks tersebut menjadi null terminator (\0) setelah ada `\n`.
  + akan mulai melakukan input dari user untuk `Perintah Saat Kontainer Dimulai` dari soal dengan logika:
    * melakukan printf `printf("Perintah Saat Kontainer Dimulai: ");` untuk memberi tahu user melakukan input.
    * melakukan perintah `fgets(request->perintah, sizeof(request->perintah), stdin);` unuk mengambil `char` dari `perintah`.
    * melakukan perintah `request->perintah[strcspn(request->perintah, "\n")] = 0;` untuk mengubah karakter di indeks tersebut menjadi null terminator (\0) setelah ada `\n`.
  + akan mulai melakukan input dari user untuk `Volume` dari soal dengan logika:
    * melakukan printf `printf("Volume: ");` untuk memberi tahu user melakukan input.
    * melakukan perintah `fgets(request->volume, sizeof(request->volume), stdin);` unuk mengambil `char` dari `volume`.
    * melakukan perintah `request->volume[strcspn(request->volume, "\n")] = 0;` untuk mengubah karakter di indeks tersebut menjadi null terminator (\0) setelah ada `\n`.
  + melakukan send ke server untuk request `docker_info` dengan logika:
    * melakukan cek untuk memeriksa `MSG_TYPE` ke server dengan perintah `request->msg_type = MSG_TYPE;`.
    * menyalin string `"docker_info"` ke dalam array `request->request_type` dengan perintah `strcpy(request->request_type, "docker_info");`.
    * menyalin nilai dari variabel `username` ke dalam array `request->username` dengan perintah `strcpy(request->username, username);`.
  + melakukan perintah if else `if (msgsnd(msgid, request, sizeof(struct msg_buffer), 0) == -1)` untuk memeriksa apakah panggilan `msgsnd()` berhasil. Fungsi `msgsnd()` mengirimkan pesan ke antrean pesan dengan ID `msgid`. Argumen pertama adalah ID antrean pesan, kedua adalah alamat dari buffer yang berisi pesan yang akan dikirim, ketiga adalah ukuran pesan, dan yang keempat adalah opsi tambahan (dalam kasus ini, 0, yang menunjukkan tidak ada opsi tambahan). Jika panggilan `msgsnd()` mengembalikan nilai -1, maka ada kesalahan, dan pesan kesalahan akan dicetak menggunakan `perror()`, diikuti dengan keluar dari program menggunakan `exit(EXIT_FAILURE)`.
  + menunggu respon dari server dengan fungsi if else `if (msgrcv(msgid, response, sizeof(struct msg_buffer), MSG_TYPE, 0) == -1)` yang disimpan dalam variabel `response`. Fungsi `msgrcv()` menunggu pesan dengan ID `msgid`.Jika panggilan `msgrcv()` mengembalikan nilai -1, maka ada kesalahan, dan pesan kesalahan akan dicetak menggunakan `perror()`, diikuti dengan keluar dari program menggunakan `exit(EXIT_FAILURE)`.
  + melakukn perintah if else untuk melakukan ce apakah server berhasil menerima `docker_info` dengan logika:
    * if else pertama, yaitu `if (response->success)` akan melakukan printf `printf("Berhasil memasukkan data docker untuk %s\n", username);` bahwa `docker_info` berhasil dinput.
    * dan if else kedua, yaitu `else` akan melakukan printf `printf("Gagal memasukkan data docker untuk %s\n", username);` bahwa `docker_info` gagal dinput.
  + membersihkan Buffer Request dan Response dengan perintah `clear_msg_buffer(request);` dan `clear_msg_buffer(response);`
- setelah itu fungsi tersebut akan dipanggi di main dengan logika:
  + deklarasi `char service_name[20];` untuk input dari user memilih service yang akan dilakukan.
  + melakukan printf `printf("Enter service name (or 'exit' to quit): ");` untuk memberi tahu apa yang harus dilakukan user.
  + melakukan perintah `fgets(service_name, sizeof(service_name), stdin);` untuk mengambil `char service_name`.
  + melakukan perintah `service_name[strcspn(service_name, "\n")] = 0;` untuk menghitung panjang line dan jika ditemukan `\n` maka akan melakukan terminate untuk menghapus line. Jadi, jika `service_name` adalah "docker_info\n", maka hasil dari `strcspn(service_name, "\n")` akan menjadi panjang "docker_info" yaitu sebelum karakter newline. dan setelah panjang string sebelum newline ditemukan, karakter di indeks tersebut diubah menjadi null terminator (\0). Ini berarti string akan terpotong, sehingga karakter newline dan semua karakter setelahnya diabaikan.
  + akan melakukan fungsi if else yang pertama, yaitu `if (strcmp(service_name, "exit") == 0)` yang akan melakukan perintah exit jika input dari user adalah exit, sesuai pemanggilan fungsi `exit_request(msgid, &request, &response);` dan akan dilakukan `break;`.
  + fungsi if else yang kedua,yaitu `else if (strcmp(service_name, "docker_info") == 0)` seperti yang diminta pada soal subtask b ini, sesuai  dengan fungsi yang dipanggil yaitu `send_docker_info_request(msgid, username, &request, &response);` untuk bagian ini sudah dijelaskan dipoint pertama yaitu pada penjelasan logika fungsi void: `void send_docker_info_request(int msgid, const char *username, struct msg_buffer *request, struct msg_buffer *response)`.
  + fungsi if else yang terakhir, yaitu `else` yang akan mencetak nama layanan yang diminta dan nomor klien di terminal, sesuai dengan perintah `printf("Requested service: %s (Client %d)\n", service_name, client_index);` didalam if else tersebut.

### Task-2 c
c. Setelah semua request service dari client terkumpul, server lalu menggabungkannya menjadi sebuah file docker compose. Lalu jalankan file docker compose yang dibuat tadi.

pada server.c:
```c
// Function to merge docker info into a Docker Compose file and execute it
void merge_and_execute_docker_compose()
{
    // Check if there's a previous Docker Compose file
    //... (berisi kode dari poin subtask d)

    FILE *compose_file = fopen("docker-compose.yml", "w");
    if (compose_file == NULL)
    {
        perror("Failed to create Docker Compose file");
        exit(EXIT_FAILURE);
    }

    // Write Docker Compose file header
    fprintf(compose_file, "version: '3'\n\nservices:\n");

    // Write service configurations for each client
    for (int i = 0; i < num_used_usernames; i++)
    {
        fprintf(compose_file, "  %s:\n", used_usernames_list[i].nama_container);
        fprintf(compose_file, "    container_name: %s\n", used_usernames_list[i].nama_container);
        fprintf(compose_file, "    image: %s\n", used_usernames_list[i].image);
        fprintf(compose_file, "    command: [\"sh\", \"-c\", \"%s\"]\n", used_usernames_list[i].perintah);
        fprintf(compose_file, "    volumes:\n");
        fprintf(compose_file, "      - %s:/var/lib/%s/\n", used_usernames_list[i].volume, used_usernames_list[i].volume);
        fprintf(compose_file, "\n");
    }

    // Write volumes configuration
    fprintf(compose_file, "volumes:\n");
    for (int i = 0; i < num_used_usernames; i++)
    {
        fprintf(compose_file, "  %s:\n", used_usernames_list[i].volume);
        fprintf(compose_file, "    driver: local\n");
    }

    fclose(compose_file);

    // Execute the Docker Compose file
    pid_t pid = fork();
    if (pid == -1)
    {
        perror("Fork failed");
        exit(EXIT_FAILURE);
    }
    else if (pid == 0)
    {
        // Child process, execute "docker-compose up -d"
        execlp("sudo", "sudo", "docker", "compose", "up", "-d", NULL);
        perror("Exec failed");
        exit(EXIT_FAILURE);
    }
    else
    {
        // Parent process, wait for child to finish
        wait(NULL);
    }

    // Reset the count of successfully received docker info
    check_num_docker_info = 0;
}

```
dari subtask c bagian client.c ini memiliki logika:
- dalam fungsi `void merge_and_execute_docker_compose()` akan menjawab soal subtask c dan d, sehingga pada awalnya kode ini akan melakukan if else untuk mengecek docker compose file apakah sudah tersedia yang lebih lengkapnya akan dijelaskan pada soal subtask d. intinya bagian awal ini untuk menghancurkan kontainer Docker yang sudah ada.
- selanjutnya untuk menjawab soal subtask c ini kodingan yang diperlukan dimulai dari perintah `FILE *compose_file = fopen("docker-compose.yml", "w");` untuk membuat dan menulis ke file Docker Compose baru jika file belum ada, dan jika ada akan melakukan open saja. 
- akan diperiksa apakah `docker_file` berhasil dibuka dengan `if (compose_file == NULL)` jika tidak maka akan melakukan `perror("Failed to create Docker Compose file");` dan melakuan exit `exit(EXIT_FAILURE);`
- selanjutnya akan mulai melakukan penulisan didalam file docker compose yang telah dibuka dengan logika:
  + menulis header `fprintf(compose_file, "version: '3'\n\nservices:\n");`.
  + menulis `service configuration` dari tiap client yang ada menggunakan for loop `for (int i = 0; i < num_used_usernames; i++)` yang didalam for loop ini akan melakukan penulisan:
    * `fprintf(compose_file, "  %s:\n", used_usernames_list[i].nama_container);`
    * `fprintf(compose_file, "    container_name: %s\n", used_usernames_list[i].nama_container);`
    * `fprintf(compose_file, "    image: %s\n", used_usernames_list[i].image);`
    * `fprintf(compose_file, "    command: [\"sh\", \"-c\", \"%s\"]\n", used_usernames_list[i].perintah);`
    * `fprintf(compose_file, "    volumes:\n");`
    * `fprintf(compose_file, "      - %s:/var/lib/%s/\n", used_usernames_list[i].volume, used_usernames_list[i].volume);`
    * `fprintf(compose_file, "\n");`
  + menulis konfigurasi volume dengan melakukan:
    * melakukan penulisan dengan perintah `fprintf(compose_file, "volumes:\n");`
    * melakukn for loop `for (int i = 0; i < num_used_usernames; i++)` yang didalamnya akan melakukan penulisan dengan perintah `fprintf(compose_file, "  %s:\n", used_usernames_list[i].volume);` dan `fprintf(compose_file, "    driver: local\n");`.
  + menutup file docker dompose dengan `fclose(compose_file);`
- melakukan execute docker compose file dengan logika:
  + melakukan deklarasi `pid_t pid = fork();`
  + melakukan if else pertama, yaitu `if (pid == -1)` untuk pemberitahuan bahwa fork gagal `perror("Fork failed");` dan melakukan exit `exit(EXIT_FAILURE);`.
  + melakukan if else kedua, yaitu `else if (pid == 0)` untuk meng eksekusi `"docker-compose up -d"` pada child process dengan logika:
    * melakukan `execlp("sudo", "sudo", "docker", "compose", "up", "-d", NULL);`
    * jika error lakukan `perror("Exec failed");` dan melakukan exit `exit(EXIT_FAILURE);`
  + melakuakn if else terakhir, yaitu `else` pada parent process untuk tida menunggu `wait(NULL);`
  + terakhir melakukan reset count dari docker info yang berhasil diterima.
  
### Task-2 d
d. Tidak sampai situ, setelah docker compose sebelumnya selesai dijalankan, client.c akan meminta prompt lagi dari user. Prompt yang diberikan sesuai dengan point (b). Jika setiap client melakukan hal yang sama pada point (b), maka server akan menghasilkan file docker compose yang baru. Lalu container yang telah dijalankan sebelumnya akan dihancurkan, yang kemudian akan digantikan oleh service terbaru yang telah di request dari client tadi.

implementasi subtask d ini merupakan satu kesatuan dengan subtask c, yang hanya mengambil pada bagian awalnya pada fungsi `void merge_and_execute_docker_compose()` karena saat membuat kode ini saya menggabungkan logika untuk merge dan execute. dan untuk kode yang diambil adalah yang bagian pertama yaitu sebagai berikut:
```c
// Function to merge docker info into a Docker Compose file and execute it
void merge_and_execute_docker_compose()
{
    // Check if there's a previous Docker Compose file
    if (access("docker-compose.yml", F_OK) != -1)
    {
        // Previous Docker Compose file exists, destroy previous containers
        pid_t pid = fork();
        if (pid == -1)
        {
            perror("Fork failed");
            exit(EXIT_FAILURE);
        }
        else if (pid == 0)
        {
            // Child process, execute "docker-compose down"
            execlp("docker-compose", "docker-compose", "down", NULL);
            perror("Exec failed");
            exit(EXIT_FAILURE);
        }
        else
        {
            // Parent process, wait for child to finish
            wait(NULL);
        }
    }
    //... (berisi kode dari poin subtask c)

```

dari subtask d bagian client.c ini memiliki logika:
- kode ini adalah jika ditemukan file docker compose, maka akan dilakukan penghancuran pada container sebelumnya.
- yang pertama adalah memeriksa apakah file "docker-compose.yml" ada, dengan perintah `access()`. `F_OK` adalah konstanta yang digunakan untuk memeriksa keberadaan file. Jika file ada, `access()` akan melakukan return 0, jika tidak, nilai yang lebih kecil dari 0 akan dikembalikan. Jadi, ekspresi `if (access("docker-compose.yml", F_OK) != -1)` memeriksa apakah file tersebut ada. didalam fungsi if else ini terdapat logika yaitu:
  + melakukan destroy dengan fork, dimulai dengan deklarasi `pid_t pid = fork();`.
  + melakukan fungsi if else yang pertama, yaitu `if (pid == -1)` jika fork gagal maka akan menjalankan `perror("Fork failed");` dan melakukan exit `exit(EXIT_FAILURE);`.
  + melakukan if else kedua, yaitu `else if (pid == 0)` jika berhasil, child process akan melakukan `execlp("docker-compose", "docker-compose", "down", NULL);`, jika tidak berhasil akan melakukan `perror("Exec failed");` dan `exit(EXIT_FAILURE);`.
  + melakukan if else yang terakhir, yaitu `else` untuk parent process tidak melakukan wait `wait(NULL);`

### _Screenshot_ Hasil
melakukan run dengan xterm : 
![Screenshot from 2024-05-11 21-19-31](https://github.com/Wadagraprana/test/assets/115563671/07cc7641-d8c6-4229-b4c2-7069179b7421)
menginput jumlah client di server: 
![Screenshot from 2024-05-11 21-22-34](https://github.com/Wadagraprana/test/assets/115563671/47148c98-07ab-4c81-9ec2-618955d6c29e)
menginput username: 
![Screenshot from 2024-05-11 21-23-31](https://github.com/Wadagraprana/test/assets/115563671/6a3ffd45-67ee-4a24-82e4-701fe84f3f72)
mencoba memasukkan docker_info pada client 1: 
![Screenshot from 2024-05-11 21-24-23](https://github.com/Wadagraprana/test/assets/115563671/21c8b461-cf85-4b18-be3d-b166cd187e7f)
mencoba memasukkan docker_info pada client 3 (client 3 dahulu untuk mengtest fungsi urutan push client yang lebih dahulu): 
![Screenshot from 2024-05-11 21-25-42](https://github.com/Wadagraprana/test/assets/115563671/17150740-a147-48cf-8ed5-1cefa9e63018)
mencoba memasukkan docker_info pada client 2: 
![Screenshot from 2024-05-11 21-26-35](https://github.com/Wadagraprana/test/assets/115563671/ca8e4ff2-e8df-4bd2-b6c6-39bb2e436912)
melakukan build container: 
![Screenshot from 2024-05-11 21-26-43](https://github.com/Wadagraprana/test/assets/115563671/e62aee50-a3e7-4ea8-b68f-a623ff2bd21f)
exit: 
![Screenshot from 2024-05-11 21-27-01](https://github.com/Wadagraprana/test/assets/115563671/4d81d014-f98d-45f0-a664-8b94147d6066)
mencoba untuk docker copose up: 
![Screenshot from 2024-05-11 21-32-15](https://github.com/Wadagraprana/test/assets/115563671/cd0686f7-061b-47ec-9c63-6519572415ed)


### Task 3

### Penjelasan Kode

chat.c for sub-task a

```c
#include <stdio.h>
#include <string.h>
#include <pthread.h>
#include <stdlib.h>
```

Inklusi header-header yang diperlukan dalam kode ini.

```c
#define MAX_FILENAME_LENGTH 32
#define MAX_LINE_LENGTH 256

char filename[MAX_FILENAME_LENGTH];
```

Didefinisikan panjang maksimum nama file, panjang maksimum satu baris dalam file, dan deklarasi variabel global `filename` untuk menyimpan nama file.

```c
void *writeArg(void *arg) {
    char *data = (char *)arg;
    FILE *file = fopen(filename, "a");
    if (file == NULL) {
        perror("Error opening file");
        pthread_exit(NULL);
    }

    fprintf(file, "%s ", data);

    fclose(file);
    pthread_exit(NULL);
}
```

Fungsi `writeArg` ditujukan untuk ditangani oleh thread. Menerima argumen `arg` yang merupakan pointer void. Argumen ini kemudian di-casting menjadi pointer ke char, mengingat bahwa data yang ditulis adalah string. Fungsi membuka file dengan nama yang tersimpan dalam variabel global filename dengan mode "a" (append) yang berarti data baru akan ditambahkan ke akhir file tanpa menghapus data yang sudah ada. Jika file tidak dapat dibuka, maka fungsi akan mencetak pesan kesalahan menggunakan `perror()` dan thread akan diakhiri menggunakan `pthread_exit()`. digunakan `fprintf()` untuk menulis data ke file. Setelah selesai menulis, file ditutup dan thread diakhiri.

```c
void writeToFile(const char *filename, char *data[], int numData) {
    pthread_t threads[numData];
    int i;

    for (i = 0; i < numData; i++) {
        if (pthread_create(&threads[i], NULL, writeArg, (void *)data[i]) != 0) {
            perror("Error creating thread");
            pthread_exit(NULL);
        }
        pthread_join(threads[i], NULL);
    }

    FILE *file = fopen(filename, "a");
    if (file == NULL) {
        perror("Error opening file");
        pthread_exit(NULL);
    }

    fprintf(file, "\n");
    fclose(file);

    printf("Pesan berhasil ditulis ke dalam file '%s'.\n", filename);
}
```

Fungsi `writeToFile` menerima argumen `filename` yang merupakan nama file target, array `data` yang berisi data yang akan ditulis, dan `numData` yang merupakan jumlah data yang akan ditulis. Fungsi ini membuat array dari thread sejumlah `numData`, menggunakan loop for untuk membuat thread sebanyak data yang akan ditulis. Setiap thread dibuat dengan memanggil `pthread_create()` yang akan menjalankan fungsi `writeArg()` dengan argumen yang sesuai. Jika pembuatan thread gagal, pesan kesalahan akan dicetak menggunakan `perror()` dan program akan berakhir menggunakan `pthread_exit()`. Setelah membuat semua thread, menggunakan `pthread_join()` untuk menunggu setiap thread selesai menulis sebelum melanjutkan eksekusi. Setelah semua thread selesai, membuka file untuk menulis karakter baris baru `\n`, menutup file, dan memberikan pesan bahwa pesan telah berhasil ditulis ke dalam file.

```c
void readFromFile(const char *filename) {
    FILE *file = fopen(filename, "r");
    if (file == NULL) {
        perror("Error opening file");
        return;
    }

    printf("Pesan yang ditulis:\n");
    char line[MAX_LINE_LENGTH];
    while (fgets(line, MAX_LINE_LENGTH, file) != NULL) {
        printf("%s", line);
    }

    fclose(file);
}
```

Fungsi `readFromFile` bertanggung jawab untuk membaca dan mencetak isi file. Fungsi ini menerima argumen `filename` yang merupakan nama file yang akan dibaca, membuka file dengan mode "r" (read). Jika file tidak dapat dibuka, maka fungsi akan mencetak pesan kesalahan dan langsung keluar. Digunakan `fgets()` untuk membaca setiap baris dari file. Setiap baris kemudian dicetak menggunakan `printf()`. Setelah selesai membaca, file ditutup.

```c
int main(int argc, char *argv[]) {
    if (strcmp(argv[1], "write") == 0) {
        if (argc >= 4) {
            strncpy(filename, argv[2], MAX_FILENAME_LENGTH - 1);
            filename[MAX_FILENAME_LENGTH - 1] = '\0';

            writeToFile(filename, &argv[3], argc - 3);
        } else {
            printf("Arguments are too short to write.\n");
            return 1;
        }
    } else if (strcmp(argv[1], "read") == 0) {
        if (argc == 3) {
            strncpy(filename, argv[2], MAX_FILENAME_LENGTH - 1);
            filename[MAX_FILENAME_LENGTH - 1] = '\0';

            readFromFile(filename);
        } else {
            printf("Invalid number of arguments for read.\n");
            return 1;
        }        
    } else {
        printf("Invalid mode. Please specify 'read' or 'write'.\n");
        return 1;
    }

    return 0;
}
```

Di `main` diperiksa argumen baris perintah. Jika argumen pertama adalah "write", maka memanggil writeToFile() dengan argumen nama file dan data yang akan ditulis. Apabila argumen pertama adalah "read", maka memanggil readFromFile() dengan argumen nama file. Namun, jika argumen pertama tidak sesuai dengan "write" atau "read", maka program akan mencetak pesan kesalahan.

chat.c after update

```c
#include <stdio.h>
#include <string.h>
#include <pthread.h>
#include <stdlib.h>

#define MAX_FILENAME_LENGTH 32
#define MAX_LINE_LENGTH 256

char filename[MAX_FILENAME_LENGTH] = "/shared-volume/";
char filenetwork[MAX_FILENAME_LENGTH] = "/shared-volume/network.txt";
```

Penambahan Variabel Global
char `filenetwork[MAX_FILENAME_LENGTH] = "/shared-volume/network.txt"`: Variabel ini menyimpan path dari file network.txt yang digunakan untuk menyimpan data jaringan.

```c
void *writeArg(void *arg) {
    char *data = (char *)arg;
    FILE *file = fopen(filename, "a");
    if (file == NULL) {
        perror("Error opening file");
        pthread_exit(NULL);
    }

    fprintf(file, "%s ", data);

    fclose(file);
    pthread_exit(NULL);
}

void writeToFile(const char *filename, char *data[], int numData, const char *container_name, char *argv[]) {
    FILE *network_file = fopen(filenetwork, "r");
    if (network_file == NULL) {
        perror("Error opening network file");
        pthread_exit(NULL);
    }

    char network_data[MAX_LINE_LENGTH][MAX_LINE_LENGTH];
    int numNetworkData = 0;
    char network_line[MAX_LINE_LENGTH];

    while (fgets(network_line, MAX_LINE_LENGTH, network_file) != NULL) {
        network_line[strcspn(network_line, "\n")] = 0;
        strcpy(network_data[numNetworkData], network_line);
        numNetworkData++;
    }

    fclose(network_file);

    int found = 0;
    for (int i = 0; i < numNetworkData; i++) {
        if (strncmp(argv[2], network_data[i], strlen(argv[2])) == 0 && strlen(argv[2]) == strlen(network_data[i]) - 1) {
            found = 1;
            break;
        }

    }

    if (found == 0) {
        printf("Error: pengguna tidak ditemukan.\n");
        return;
    } else {
        pthread_t threads[numData];
        int i;

        for (i = 0; i < numData; i++) {
            if (pthread_create(&threads[i], NULL, writeArg, (void *)data[i]) != 0) {
                perror("Error creating thread");
                pthread_exit(NULL);
            }

            pthread_join(threads[i], NULL);
        }

        FILE *file = fopen(filename, "a");
        if (file == NULL) {
            perror("Error opening file");
            pthread_exit(NULL);
        }

        fprintf(file, "from %s\n", container_name);
        fclose(file);
        
        printf("Pesan berhasil dikirim.\n");
    }

}
```

Perubahan pada Fungsi `writeToFile()`
Penambahan Argumen: Ditambahkan argumen `const char *container_name` untuk menyimpan nama kontainer, `char *argv[]` untuk mendapatkan argumen dari baris perintah.
Validasi Pengguna: Dibaca konten dari file `network.txt` untuk memeriksa apakah pengguna yang dimaksud ada dalam jaringan. Jika pengguna tidak ditemukan, program akan mencetak pesan kesalahan.
Penulisan ke File: Jika pengguna ditemukan, thread akan dibuat untuk menulis data ke file. Setelah selesai menulis, informasi tambahan `from {container_name}` akan ditambahkan ke dalam file. Pesan keberhasilan akan dicetak.

```c
void copyFile(const char *sourceFilename, const char *destinationFilename) {
    FILE *sourceFile = fopen(sourceFilename, "r");
    if (sourceFile == NULL) {
        perror("Error opening source file");
        return;
    }

    FILE *destinationFile = fopen(destinationFilename, "w");
    if (destinationFile == NULL) {
        perror("Error opening destination file");
        fclose(sourceFile);
        return;
    }

    char line[MAX_LINE_LENGTH];
    while (fgets(line, MAX_LINE_LENGTH, sourceFile) != NULL) {
        fputs(line, destinationFile);
    }

    fclose(sourceFile);
    fclose(destinationFile);
}
```

Fungsi `copyFile` dibuat untuk menyalin isi dari satu file ke file lainnya. Diperlukan untuk menyimpan salinan pesan yang dikirim ke file `chatbox.txt`.

```c
void readFromFile(const char *container_name) {
    char full_filename[64] = "/shared-volume/";

    strcat(full_filename, container_name);
    strcat(full_filename, ".txt");

    FILE *file = fopen(full_filename, "r");
    if (file == NULL) {
        system("cat chatbox.txt");
        return;
    }

    fclose(file);

    copyFile(full_filename, "/chatbox.txt");

    file = fopen("chatbox.txt", "r");
    if (file == NULL) {
        perror("Error opening file");
        return;
    }

    char line[MAX_LINE_LENGTH];
    while (fgets(line, MAX_LINE_LENGTH, file) != NULL) {
        printf("%s", line);
    }

    fclose(file);
}
```

Perubahan pada Fungsi `readFromFile()`
Penambahan Path File: Path file yang dibaca sekarang disesuaikan dengan format `/shared-volume/{container_name}.txt`.
Penyalinan File: Jika file yang dituju tidak ditemukan, isi dari `chatbox.txt` akan ditampilkan sebagai gantinya. Fungsi `copyFile()` digunakan untuk menyalin isi file yang dituju ke `chatbox.txt`.

```c
int main(int argc, char *argv[]) {
    const char *container_name = getenv("container_name");
    if (strcmp(argv[1], "write") == 0) {
        if (argc >= 4) {
            strcat(filename, argv[2]);
            strcat(filename, ".txt");

            writeToFile(filename, &argv[3], argc - 3, container_name, argv);
        } else {
            printf("Arguments are too short to write.\n");
            return 1;
        }
    } else if (strcmp(argv[1], "read") == 0) {
        if (argc == 2) {
            readFromFile(container_name);
        } else {
            printf("Invalid number of arguments for read.\n");
            return 1;
        }        
    } else {
        printf("Invalid mode. Please specify 'read' or 'write'.\n");
        return 1;
    }

    return 0;
}
```

Perubahan pada `main()`
Menggunakan `getenv()`: Variabel `container_name` diinisialisasi dengan nilai dari environment `variable container_name`.
Penggunaan Nama Kontainer: Nama kontainer digunakan sebagai argumen tambahan dalam pemanggilan fungsi `writeToFile()` dan `readFromFile()`.
Penyusunan Nama File: Nama file yang akan ditulis sekarang disusun dengan menambahkan ekstensi `.txt` setelah nama pengguna. Nama file yang akan dibaca juga disusun dengan menambahkan ekstensi `.txt` setelah nama kontainer.

pengaturan.c

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
```

Inklusi header-header yang dibutuhkan dalam kode ini.

```c
#define MAX_LINE_LENGTH 256
#define NETWORK_FILE "network.txt"
```

Didefinisikan `MAX_LINE_LENGTH` sebagai panjang maksimum dari sebuah baris dalam file (256), dan `NETWORK_FILE` sebagai nama file yang digunakan untuk menyimpan informasi jaringan.

```c
void connectedContainer(char *container_name) {
    FILE *file = fopen(NETWORK_FILE, "w");
    if (file == NULL) {
        printf("Failed to open file.\n");
        return;
    }

    char command[100];
    snprintf(command, sizeof(command), "sh -c \"docker network inspect indihome --format='{{range .Containers}}{{.Name}}{{println}}{{end}}'\"");

    FILE *pipe = popen(command, "r");
    if (pipe == NULL) {
        printf("Failed to run command.\n");
        fclose(file);
        return;
    }

    char line[MAX_LINE_LENGTH];
    while (fgets(line, sizeof(line), pipe) != NULL) {
        line[strcspn(line, "\n")] = 0;
        fprintf(file, "%s\n", line);
    }

    fclose(file);
    pclose(pipe);

    char copy_command[256];
    snprintf(copy_command, sizeof(copy_command), "docker cp %s %s:/shared-volume/network.txt", NETWORK_FILE, container_name);
    system(copy_command);
}
```

Fungsi `connectedContainer` bertanggung jawab untuk mendapatkan daftar kontainer yang terhubung ke sebuah jaringan Docker dan menyimpannya ke dalam sebuah file. Pertama, dibuka file `NETWORK_FILE` untuk ditulis. Lalu dibuat sebuah perintah menggunakan `snprintf()` yang akan mengeksekusi perintah shell `docker network inspect indihome --format='{{range .Containers}}{{.Name}}{{println}}{{end}}` untuk mendapatkan daftar kontainer yang terhubung ke netwprk `indihome`. Dibuka sebuah proses pipe menggunakan `popen()` untuk mengeksekusi perintah tersebut, dan membaca keluaran dari proses pipe dan menuliskannya ke dalam file. Setelah selesai, tutup file dan proses pipe. Kemudian dibuat perintah untuk menyalin file `NETWORK_FILE` ke dalam kontainer dengan nama yang diberikan menggunakan `snprintf(copy_command, sizeof(copy_command), "docker cp %s %s:/shared-volume/network.txt", NETWORK_FILE, container_name)` dan `system(copy_command)`.

```c
int main(int argc, char *argv[]) {
    if (argc == 2) {
        if (strcmp(argv[1], "list") == 0) {
            system("docker ps -a");
        } else {
            printf("Argument are invalid.\n");
        }
    } else if (argc == 3) {
        if (strcmp(argv[1], "create") == 0) {
            char command[100];
            snprintf(command, sizeof(command), "docker create -e container_name=%s --name %s -i -v task3:/shared-volume dockermessage:v2", argv[2], argv[2]);
            system(command);
        } else if (strcmp(argv[1], "delete") == 0) {
            char command[100];
            snprintf(command, sizeof(command), "docker rm  %s", argv[2]);
            system(command);
        } else if (strcmp(argv[1], "login") == 0) {
            char command[100];
            snprintf(command, sizeof(command), "docker start %s", argv[2]);
            system(command);
        } else {
            printf("Argument are invalid.\n");
        }
    } else if (argc == 4) {
        if (strcmp(argv[1], "connect") == 0) {
            char command1[100];
            char command2[100];
            snprintf(command1, sizeof(command1), "docker network connect indihome %s", argv[2]);
            snprintf(command2, sizeof(command2), "docker network connect indihome %s", argv[3]);
            system(command1);
            system(command2);
            connectedContainer(argv[2]);
        } else if (strcmp(argv[1], "unconnect") == 0) {
            char command1[100];
            char command2[100];
            snprintf(command1, sizeof(command1), "docker network disconnect indihome %s", argv[2]);
            snprintf(command2, sizeof(command2), "docker network disconnect indihome %s", argv[3]);
            system(command1);
            system(command2);
            connectedContainer(argv[2]);
        } else {
            printf("Argument are invalid.\n");
        }
    } else {
        printf("Number of arguments are invalid.\n");
    }

    return 0;
}
```

Fungsi `main` program.
Pertama, diperiksa jumlah argumen baris perintah.
Jika jumlah argumen adalah 2: apabila argumen kedua adalah `list`, ditampilkan daftar kontainer Docker menggunakan perintah `docker ps -a`. Jika bukan, mencetak pesan kesalahan.
Jika jumlah argumen adalah 3: apabila argumen pertama adalah `create`, dibuat sebuah kontainer Docker dengan nama yang diberikan menggunakan perintah `docker create`. Jika argumen pertama adalah `delete`, akan menghapus sebuah kontainer Docker dengan nama yang diberikan menggunakan perintah `docker rm`. Jika argumen pertama adalah `login`, akan memulai sebuah kontainer Docker dengan nama yang diberikan menggunakan perintah `docker start`. Apabila bukan semua, akan mencetak pesan kesalahan.
Jika jumlah argumen adalah 4: Jika argumen pertama adalah `connect`, menghubungkan dua kontainer Docker ke dalam jaringan indihome menggunakan perintah `docker network connect`. Jika argumen pertama adalah `unconnect`, memutuskan hubungan dua kontainer Docker dari jaringan indihome menggunakan perintah `docker network disconnect`. Setelah itu, memanggil fungsi `connectedContainer()` untuk menyimpan daftar kontainer yang terhubung ke dalam file. Jika tidak ada dari yang telah disebutkan, akan mencetak pesan kesalahan.
Jika jumlah argumen tidak sesuai, akan dicetak pula pesan kesalahan.

Dockerfile
```
FROM alpine:3.14
```

Baris ini mendefinisikan image dasar yang akan digunakan untuk membangun image Docker yang baru. Dalam hal ini, image dasar yang digunakan adalah alpine:3.14, yang merupakan versi 3.14 dari Alpine Linux. Alpine Linux adalah distribusi Linux yang ringan dan sering digunakan sebagai dasar untuk image Docker karena ukurannya yang kecil.

```
RUN apk add build-base
```

Baris ini menjalankan perintah shell untuk menambahkan package build-base ke dalam image. Package ini biasanya berisi alat dan pustaka yang diperlukan untuk melakukan kompilasi program C atau C++, seperti `gcc`, `make`, dan lain-lain.

```
COPY chat.c .
```

Baris ini menyalin file `chat.c` dari direktori lokal (di mana Dockerfile berada) ke dalam direktori kerja di dalam image Docker. Tanda titik (.) mengindikasikan bahwa file akan disalin ke dalam direktori kerja saat ini di dalam image.

```
RUN gcc chat.c -o chat
```

Baris ini menjalankan perintah shell untuk mengkompilasi file `chat.c` menjadi sebuah executable dengan nama chat. Ini dilakukan dengan menggunakan compiler GNU Compiler Collection (GCC) yang sudah diinstal sebelumnya di langkah sebelumnya.

```
RUN touch chatbox.txt
```

Baris ini menjalankan perintah shell untuk membuat file kosong bernama `chatbox.txt` di dalam direktori kerja di dalam image Docker. Perintah `touch` digunakan untuk membuat file atau memperbarui waktu akses dan modifikasi dari file jika file sudah ada.

### _Screenshot_ Hasil

#### Sub-Task a
![01](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/c8b37329-b860-44be-917a-1053eb6ca6b0)

#### Sub-Task b
Build dockermessage

![02](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/98eb4dca-dafd-44a5-98e4-f5c32126d02c)
![03](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/fc6e32d0-49a8-4266-afd2-853bfc74e459)

#### Sub-Task c dan d
Build dockermessage:v2

![04](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/6cc642de-3fff-415a-bd09-8b2738533028)

Mencoba seluruh menu dari `./pengaturan`.

![05](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/a97c148c-5edb-430d-8573-80bb4604699e)
![06](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/3fd15e42-716e-4862-a380-f31815ab8489)
![07](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/4b48caaf-6392-4904-9449-998378ef1664)

Mencoba mengirim pesan saat dua container connect.

![08](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/737fb21a-b658-48df-bc8f-8cca92d7bdfa)
![09](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/b8e46ff2-6be6-4f29-b094-0db0bb93e1ee)
![10](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/2b95fcbf-6188-41b5-be31-92c2e46bf76a)

Mencoba mengirim pesan saat dua container unconnect.

![11](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/497c01e8-0db4-47f3-820a-ce60d446e181)
![12](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/219065b6-93d7-409a-a7ee-1524431a1bbb)
![13](https://github.com/sisop-its-s24/praktikum-modul-3-e06/assets/126127409/7c44c12b-0184-4af1-a34b-b6aef0b3b1a7)

### Task 4

### Penjelasan Kode

dockerfile
```
FROM php:8.2-fpm

# Copy composer.lock and composer.json
COPY /laravel-app/composer.lock /laravel-app/composer.json /var/www/laravel-app/

# Set working directory
WORKDIR /var/www/laravel-app

# Update apt and install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    libonig-dev \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl \
    libzip-dev

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl gd

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Set COMPOSER_ALLOW_SUPERUSER to allow plugins to run as root
ENV COMPOSER_ALLOW_SUPERUSER=1

# Copy the rest of the application into the container
COPY /laravel-app .

USER root

RUN chmod 777 -R /var/www/laravel-app
# Generate application key
RUN php artisan key:generate

# Create symbolic link for storage
RUN php artisan storage:link

# Set appropriate permissions
RUN chmod -R 777 storage bootstrap/cache

# Expose the port and start php-fpm
EXPOSE 9090
CMD ["php-fpm"]
```

docker-compose.yml
```
version: '3.1'
services:

  # PHP Service
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: app
    restart: unless-stopped
    tty: true
    environment:
      SERVICE_NAME: app
      SERVICE_TAGS: dev
    working_dir: /var/www/laravel-app
    volumes:
      - ./laravel-app:/var/www/laravel-app  # Mengubah jalur volume sesuai dengan lokasi proyek Laravel
      - ./php/local.ini:/usr/local/etc/php/conf.d/local.ini
    networks:
      - app-network

  # Nginx Service
  webserver:
    image: nginx:alpine
    container_name: webserver
    restart: always
    tty: true
    ports:
      - "9090:9090"
    volumes:
      - ./laravel-app:/var/www/laravel-app  # Mengubah jalur volume sesuai dengan lokasi proyek Laravel
      - ./nginx/conf.d/app.conf:/etc/nginx/conf.d/app.conf
    networks:
      - app-network

  # MySQL Service
  db:
    image: mysql:latest
    container_name: db
    restart: always
    tty: true
    ports:
      - "3306:3306"
    environment:
      MYSQL_DATABASE: keyisa
      MYSQL_ALLOW_EMPTY_PASSWORD: 1
    volumes:
      - dbdata:/var/lib/mysql/
      - ./mysql/my.cnf:/etc/mysql/my.cnf
    networks:
      - app-network

# Docker Networks
networks:
  app-network:
    driver: bridge

# Volumes
volumes:
  dbdata:
    driver: local
```

### Task-4 a
- a. Akses URL berikut untuk mendapatkan resource yang dibutuhkan, yaitu sebuah aplikasi yang sedang dibuat oleh Shaniz696969!

  https://drive.google.com/file/d/1iMCSmUzlQ1OqIU72o9IGbPMeghFbj_zK/view?usp=sharing

  _It’s kinda legit, right?_ Aneh juga ya dia tidak pakai GitHub atau semacamnya tapi malah pakai Google Drive (kocak). Tapi _gapapa_ lah ya. Nah, tugas kamu adalah untuk membuat image dari aplikasi tersebut!

jawaban: Pada subtask a ini, kita harus mendownload file gdrive tersebut dan nantinya akan digunakan untuk template .env yang akan kita gunakan untuk membuat settings docker-compose.yml.

### Task-4 b
- b. Selanjutnya, karena Shaniz696969 mengatakan bahwa aplikasi ini menggunakan relational database berupa MySQL, mau tidak mau kamu harus membuat container yang menjalankan image dari MySQL! Pastikan container berjalan sebagai daemon.

  **Psst, ingat, kamu bisa mendapatkan image MySQL dari Docker Hub.**

jawaban: menggunakan settingan:
```
# MySQL Service
  db:
    image: mysql:latest
    container_name: db
    restart: always
    tty: true
    ports:
      - "3306:3306"
    environment:
      MYSQL_DATABASE: keyisa
      MYSQL_ALLOW_EMPTY_PASSWORD: 1
    volumes:
      - dbdata:/var/lib/mysql/
      - ./mysql/my.cnf:/etc/mysql/my.cnf
    networks:
      - app-network
```
### Task-4 c
- c. Kamu ingat bahwa sudah membuat image dari aplikasi Shaniz696969? Nah, sekarang, jalankan image tersebut sebagai container. Pastikan container berjalan sebagai daemon.

### Task-4 d
- d. Apa lagi yang kurang? Yup, kamu harus melakukan koneksi antara aplikasi backend tersebut dengan database yang telah dijalankan. Tentunya, dalam container yang telah kita jalankan di poin-poin sebelumnya, kan? Nah, lakukan koneksi antara dua container tersebut dalam satu network bernama **Shani-net**!

  **Hint: [belum tersedia] [ada, tapi nanti]**

jawaban: mengunakan network:
```
//diletakkan pada app, db, dan php
networks:
    - app-network
```
```
# Docker Networks
networks:
  app-network:
    driver: bridge
```
### Task-4 e
- e. Terakhir, kamu harus lakukan setup pada aplikasi backend tersebut dengan menggunakan dokumentasi Laravel sebagai panduan kalian! Aplikasi harus bisa diakses melalui http://localhost:9090. Agar lebih legit, kalian bisa melakukan hit pada endpoint `/api/province`.

jwaban:
`docker exec -it app php artisan migrate`
`docker exec -it app php artisan db:seed`
`curl -X GET http://localhost:9090/api/province`

### _Screenshot_ Hasil

melakukan docker compose up:
![Screenshot 2024-05-11 220053](https://github.com/Wadagraprana/test/assets/115563671/3ad39c6d-0326-4453-b68e-75c94822ec31)
mengeek db connection di mysql workbench:
![Screenshot 2024-05-11 220220](https://github.com/Wadagraprana/test/assets/115563671/cd722a5b-31c3-4e89-b1d9-e8ead7f9b469)
mengecek apakah sudah masuk db keysia:
![Screenshot 2024-05-11 220229](https://github.com/Wadagraprana/test/assets/115563671/21a6e6ed-c9b0-4b14-80a4-0560049a3f2d)
melakukan `docker exec -it app php artisan migrate`:
![Screenshot 2024-05-11 220319](https://github.com/Wadagraprana/test/assets/115563671/6093bfdb-4190-4940-9d50-133ca1c09f47)
melakukan `docker exec -it app php artisan db:seed`:
![Screenshot 2024-05-11 220340](https://github.com/Wadagraprana/test/assets/115563671/3f7dd303-f99d-41da-8ab0-d7276ae9a709)
melakukan `curl -X GET http://localhost:9090/api/province`:
![Screenshot 2024-05-11 220353](https://github.com/Wadagraprana/test/assets/115563671/577fe94c-fd8f-4397-8a10-2791b08fe943)
mengecek di `http://localhost:9090/`:
![Screenshot 2024-05-11 220404](https://github.com/Wadagraprana/test/assets/115563671/47fc6592-1bbd-49ed-806e-5fc254250101)






















# Deploy ke Railway.app

## Langkah 1: Buat Database MySQL di Railway

1. Buka https://railway.app
2. Login dengan GitHub
3. Klik **"New Project"** → **"Provision MySQL"**
4. Tunggu sampai database ready
5. Buka tab **"MySQL"** → Copy **Connection URL**

Contoh:
```
mysql://user:password@host:port/database
```

## Langkah 2: Import Database

1. Di Railway dashboard, klik **"MySQL"** → **"Executions"**
2. Copy paste isi file `Data Base school.sql`
3. Atau gunakan MySQL client:
```bash
mysql -h host -u user -p database < "Data Base school.sql"
```

## Langkah 3: Buat PHP API untuk Railway

Website ini perlu diubah jadi PHP lagi untuk koneksi ke database Railway.

### Buat file koneksi.php:
```php
<?php
class Database {
    private static $instance = null;
    private $connection;
    
    private $host = 'containers-us-west-123.railway.app';
    private $port = '12345';
    private $dbname = 'railway';
    private $user = 'root';
    private $pass = 'xxxxx';
    
    public static function getInstance() {
        if (!self::$instance) {
            self::$instance = new Database();
        }
        return self::$instance;
    }
    
    private function __construct() {
        $this->connection = new mysqli(
            $this->host,
            $this->user,
            $this->pass,
            $this->dbname,
            $this->port
        );
        
        if ($this->connection->connect_error) {
            die("Connection failed: " . $this->connection->connect_error);
        }
    }
    
    public function getConnection() {
        return $this->connection;
    }
    
    public function query($sql, $params = []) {
        if (empty($params)) {
            return $this->connection->query($sql);
        }
        
        $stmt = $this->connection->prepare($sql);
        if (!$stmt) {
            return false;
        }
        
        $types = str_repeat('s', count($params));
        $stmt->bind_param($types, ...$params);
        $stmt->execute();
        return $stmt->get_result();
    }
    
    public function fetch($result) {
        return $result->fetch_assoc();
    }
    
    public function fetchAll($result) {
        $rows = [];
        while ($row = $result->fetch_assoc()) {
            $rows[] = $row;
        }
        return $rows;
    }
    
    public function numRows($result) {
        return $result->num_rows;
    }
}

function db() {
    return Database::getInstance();
}
?>
```

## Langkah 4: Setup Environment Variables

Di Railway dashboard:
1. Buka **Settings** → **Environment Variables**
2. Tambahkan:
```
MYSQL_HOST=containers-us-west-123.railway.app
MYSQL_PORT=12345
MYSQL_DATABASE=school
MYSQL_USER=root
MYSQL_PASSWORD=xxxxx
```

## Langkah 5: Deploy PHP App

1. Buat file `railway.json`:
```json
{
  "$schema": "https://railway.app/railway.json",
  "build": {
    "builder": "NIXPACKS"
  },
  "deploy": {
    "numReplicas": 1,
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 10
  }
}
```

2. Push ke GitHub
3. Di Railway, **"New Project"** → **"Deploy from GitHub repo"**
4. Pilih repo `WEB-Sekolah`
5. Railway会自动检测PHP并部署

## Struktur Database

### Tabel `akun`
| Column | Type |
|--------|------|
| username | VARCHAR(50) PK |
| password | VARCHAR(50) |
| level | INT(1) |

### Tabel `guru`
| Column | Type |
|--------|------|
| nip | CHAR(10) PK |
| nama_guru | VARCHAR(50) |
| no_hp | VARCHAR(30) |
| jenkel | VARCHAR(10) |
| agama | VARCHAR(10) |
| username | VARCHAR(50) FK |

### Tabel `murid`
| Column | Type |
|--------|------|
| nisn | CHAR(10) PK |
| nama_murid | VARCHAR(50) |
| kota | VARCHAR(30) |
| jenkel | VARCHAR(10) |
| agama | VARCHAR(10) |
| kelas | INT(2) |
| jurusan | VARCHAR(3) |
| username | VARCHAR(50) FK |

### Tabel `mata_pelajaran`
| Column | Type |
|--------|------|
| kode_mata_pelajaran | VARCHAR(50) PK |
| nama_matapelajaran | VARCHAR(50) |
| kelas | INT(2) |
| jurusan | VARCHAR(3) |
| nip | CHAR(10) FK |

### Tabel `nilai`
| Column | Type |
|--------|------|
| id | INT AUTO_INCREMENT PK |
| nama | VARCHAR(50) |
| kelas | VARCHAR(2) |
| jurusan | VARCHAR(3) |
| mata_pelajaran | VARCHAR(50) |
| nilai_uts | INT |
| nilai_uas | INT |

### Tabel `pesan`
| Column | Type |
|--------|------|
| Tanggal | VARCHAR(10) |
| Subject | VARCHAR(255) |
| Nama | VARCHAR(50) |
| Email | VARCHAR(200) |
| No_HP | INT |
| Isi | TEXT |

## Demo Account

| Username | Password | Level |
|----------|----------|-------|
| admin | 1 | Admin |
| Kirito | 1 | Admin |
| roma | 1 | Guru |
| hendika | 1 | Guru |
| udin | 1 | Murid |
| Naruto | 1 | Murid |

> Password di-hash dengan MD5 (untuk demo purposes)

---

## Troubleshooting

### Error: "Connection refused"
- Pastikan port MySQL benar
- Cek Railway MySQL connection string

### Error: "Database not found"
- Buat database `school` di Railway MySQL
- Atau update dbname di config

### Error: "Access denied"
- Cek username/password di Environment Variables
# library_management_system_pacmann_project

# tujuan pengerjaan project :
pembuatan sistem manajemen perpustakaan

# detail task (lebih lengkapnya lihat code pada file library.py) :
## importing library
import mysql.connector
import random
import pandas as pd

## connect database
mydb = mysql.connector.connect(
    host = "localhost",
    user = "root",
    password = "rianto08",
    database = "lms",
)

## sintaks untuk melakukan pendaftaran user
def adduser():
    id_user = random.randint(1,1000)
    nama = input("Masukkan nama user:")
    tgl_lahir = input("Masukkan tgl lahir(YYYY-MM-DD):")
    pekerjaan = input("Masukkan pekerjaan:")
    alamat = input("Masukkan alamat:")
    data = (id_user,nama,tgl_lahir,pekerjaan,alamat)
    sql = "insert into user values(%s,%s,%s,%s,%s);"
    c = mydb.cursor()
    c.execute(sql,data)
    mydb.commit()
    print("...........")
    print("Data berhasil dimasukkan")
    main()
    
## sintaks untuk melakukan pendaftaran buku
def addbook():
    id_buku = input("Masukkan id buku:")
    nama_buku = input("Masukkan nama buku:")
    kategori = input("Masukkan kategori buku:")
    stock = input("Masukkan stock buku:")
    data = (id_buku,nama_buku,kategori,stock)
    sql = "insert into buku values(%s,%s,%s,%s);"
    c = mydb.cursor()
    c.execute(sql,data)
    mydb.commit()
    print(".........")
    print("Data berhasil dimasukkan")
    main()
    
## sintaks untuk melakukan peminjaman buku
def issuebook():
    id_user = input("Masukkan id user:")
    id_buku = input("Masukkan id buku:")
    nama_peminjam = input("Masukkan nama peminjam:")
    nama_buku = input("Masukkan nama buku:")
    tgl_peminjaman = input("Masukkan tgl peminjaman(YYYY-MM-DD):")
    sql = "insert into pinjam values(%s,%s,%s,%s,%s);"
    data = (id_user,id_buku,nama_peminjam,nama_buku,tgl_peminjaman)
    c = mydb.cursor()
    c.execute(sql,data)
    mydb.commit()
    print("............")
    print("Buku dipinjamkan ke:",nama_peminjam)
    stokbuku(id_buku,-1)
    main()
    
## sintaks untuk melakukan pengembalian buku
def submitbook():
    id_user = input("Masukkan id user:")
    id_buku = input("Masukkan id buku:")
    nama_peminjam = input("Masukkan nama peminjam:")
    nama_buku = input("Masukkan nama buku:")
    tgl_pengembalian = input("Masukkan tgl pengembalian(YYYY-MM-DD):")
    sql = "insert into kembali values(%s,%s,%s,%s,%s);"
    data = (id_user,id_buku,nama_peminjam,nama_buku,tgl_pengembalian)
    c = mydb.cursor()
    c.execute(sql,data)
    mydb.commit()
    print(".............")
    print("Buku dikembalikan oleh:",nama_peminjam)
    stokbuku(id_buku,1)
    main()
    
## sintaks untuk penambahan/pengurangan stok buku jika buku dikembalikan/dipinjam
def stokbuku(id_buku,u):
    sql = "select stock from buku where id_buku = %s;"
    data = (id_buku,)
    c = mydb.cursor()
    c.execute(sql,data)
    result = c.fetchone()
    t = result[0] + u
    sql = "update buku set stock = %s where id_buku = %s;"
    d = (t,id_buku)
    c.execute(sql,d)
    mydb.commit()
    main()
    
## sintaks untuk menampilkan daftar user
def displayuser():
    sql = "select * from user;"
    c = mydb.cursor()
    c.execute(sql)
    result = c.fetchall()
    result = pd.read_sql(sql,con=mydb)
    print(result)
    main()
    
## sintaks untuk menampilkan daftar buku
def displaybook():
    sql = "select * from buku;"
    c = mydb.cursor()
    c.execute(sql)
    result = c.fetchall()
    result = pd.read_sql(sql,con=mydb)
    print(result)
    main()

## sintaks untuk menampilkan daftar peminjaman
def displaypeminjaman():
    sql = "select * from pinjam;"
    c = mydb.cursor()
    c.execute(sql)
    result = c.fetchall()
    result = pd.read_sql(sql,con=mydb)
    print(result)
    main()
    
## sintaks untuk mencari buku
def caribuku():
    nama_buku = input("Masukkan nama buku yang dicari:")
    sql = "select * from buku where nama_buku = %s;"
    data = (nama_buku,)
    c = mydb.cursor()
    c.execute(sql,data)
    result = c.fetchall()
    result = pd.read_sql(sql,con=mydb)
    print(result)
    main()
    
## sintaks menampilkan seluruh histori transaksi
def transaksi():
    sql = """select id_user, id_buku, nama_user, nama_buku, tgl_peminjaman, tgl_pengembalian from pinjam
    join kembali using (id_user, id_buku, nama_user, nama_buku)
    group by tgl_pengembalian;
    """
    c = mydb.cursor()
    c.execute(sql)
    result = c.fetchall()
    result = pd.read_sql(sql,con=mydb)
    print(result)
    main()
    
## sintaks interface awal
def main():
    print("""...............LIBRARY MANAGEMENT SYSTEM.................
    1. Pendaftaran User Baru
    2. Pendaftaran Buku Baru
    3. Peminjaman
    4. Tampilkan Daftar User
    5. Tampilkan Daftar Buku
    6. Tampilkan Daftar Peminjaman
    7. Cari Buku
    8. Pengembalian
    9. Histori Transaksi
    0. Exit
    """)
    choice = input("Masukkan Nomor Tugas:")
    print("..........................")
    if(choice == '1'):
        adduser()
    elif(choice == '2'):
        addbook()
    elif(choice == '3'):
        issuebook()
    elif(choice == '4'):
        displayuser()
    elif(choice == '5'):
        displaybook()
    elif(choice == '6'):
        displaypeminjaman()
    elif(choice == '7'):
        caribuku()
    elif(choice == '8'):
        submitbook()
    elif(choice == '9'):
        transaksi()
    elif(choice == '0'):
        exit()
    else:
        print("Input Salah")
        main()

main()

# Cara Running Program :
1. Import dataset "lms.sql" ke mySQL Workbench dengan cara klik 'server' lalu 'data import'
2. setelah dataset di import, masuk ke terminal anaconda dan arahkan ke directory hasil download. command : cd "<directory_anda>"
3. running library.py dengan cara command : python library.py
4. lakukan task dengan memilih nomor input

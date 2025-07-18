call "D:\soft\dev\visual studio 2022\VC\Auxiliary\Build\vcvars64.bat"
 
cd D:\soft\dev\postgresql\pgvector\pgvector-master
 
set "PGROOT=C:\Program Files\PostgreSQL\16"
 
nmake /F Makefile.win
nmake /F Makefile.win install
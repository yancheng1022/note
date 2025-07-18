call "D:\soft\dev\visual studio 2022\VC\Auxiliary\Build\vcvars64.bat"
cd D:\soft\dev\postgresql\pgvector\pgvector-master
set "PGROOT=D:\soft\dev\postgresql\soft"
nmake /F Makefile.win
nmake /F Makefile.win install
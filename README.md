/*
Até 2020 a ferramenta não fazia distinção entre coletar os traces (perfumem e mmap) e a realização do bind(mbind syscall).

Em 2021 eu decidir fazer essa separação uma vez que isso permite fazer uso da ferramenta mesmo em ambientes onde não existem o intel optane.

Por isso nós temos dois branches:

- collect
- bind

*/


########
Parte 1
########

Pré-requisitos
---------------
	* sudo apt-get install libgomp1-dbg.  (Resolução de símbolos da libgomp)
	* sudo apt-get install linux-tools-common linux-tools-generic linux-tools-`uname -r`
	* pip3 install pandas matplot

- Com a divisão da ferramenta em duas partes, a primeira parte é executada sem a necessidade de configurar o optane como numanode
	* Isso significa que não é necessário o kernel superior ao 5.1 
	* ldd (Ubuntu GLIBC 2.27-3ubuntu1.2) 2.27 - Essa é a libc que funciona para interceptar o mmap com a construção do callstack. Ela está contida no ubuntu 18.04.
	* O ubuntu 20 vem com uma libc que não funciona com a nossa syscall_intercept (nao constrói o callstack). Por isso nós devemos linkar com uma libc(2.27) avulsa.

- Configurações necessárias
	* Compilar a aplicação com a flag "-g"
	* Instalar a ferramenta syscall_intercept ( step-by-step está na pasta tools)
	* Usar a biblioteca compartilhada mmap_intercept.so que é criada usando a API da syscall_intercept
	


########
Parte 2
########


Pré-requisitos
---------------
	* Instanciar o optane como numanode


- Configurações necessárias
	* Se a libc instalada for 2.31-32, será necessário linkar com a versão inferir (2.27)
		-> Nesse caso, tanto a aplicação (por exemplo gapps), quanto o numactl e o syscall_intercept devem usar a mesma libc.
		-> O código do syscall_intercept e da aplicação essa linhagem pode ser alterada no makefile. Já o numactl essa alteração é feita dentro do próprio binário.
 

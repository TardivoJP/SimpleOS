# SimpleOS

## Objetivo
 Criar um sistema operacional simples para aprender e compreender melhor o funcionamento de um SO, seguindo o projeto do KiddieOS.Community na playlist: https://www.youtube.com/watch?v=Jws7BHrts6g&list=PLsoiO2Be-2z8BfsSkspJfDiuKeC9-LSca&index=1
 
## Ferramentas Utilizadas

| Ferramenta | Link |
| ------ | ------ |
| NASM | https://www.nasm.us/ |
| Rufus | https://rufus.ie/en/ |
| FergoRaw | https://www.fergonez.net/softwares/fraw |
| VirtualBox | https://www.virtualbox.org/wiki/Download_Old_Builds_5_2 |

## Desenvolvimento
### 1 - Criando o "Hello world!"

* Criando arquivo "boot.asm" na linguagem x86 assembly com algumas intruções peculiares:
	* BITS 16: Como trata-se de um SO simples, esse comando estipula que o assembler gere código para um processador 16-bits.
	* ORG 0x7C00: Aqui especificamos o ponto de origem do código, que é o endereço de memória 0x7C00. É aqui que o BIOS carrega o código de inicialização.
	* call HelloWorld e call JumpLine: São as chamadas das sub-rotinas.
	* sub-rotina HelloWorld: Esta sub-rotina imprime os caracteres "Hello, world!" para a tela usando interrupções do BIOS (int 10h). Ela carrega cada caractere no registrador AL e então chama a interrupção 0x10 com AH=0x0E.
	* sub-rotina JumpLine: Esta sub-rotina move o cursor para a próxima linha imprimindo um caractere de nova linha (0ah) seguido por um retorno carriage (0dh).
	* times 519 - ($-$$) db 0: Esta diretiva preenche o código para garantir que ele tenha exatamente 512 bytes (o tamanho de um setor de inicialização), terminando no byte 0x1FE com o número 0xAA55 (dw 0xAA55).

* Configuramos as PATH variables do windows corretamente para a VirtualBox e o NASM pelo painel de controle.
* Utilizamos o command prompt do windows na mesma pasta do arquivo "boot.asm" e executamos esta linha para gerar o arquivo binário:

```sh
nasm -f bin boot.asm -o boot.bin
```

* Utilizamos o command prompt do windows na mesma pasta do arquivo "boot.bin" e executamos esta linha para gerar o arquivo de imagem:

```sh
VBoxManage convertfromraw boot.bin boot.img --format raw
```

* Abrimos o VirtualBox e criamos uma máquina virtual extremamente simples, sem ISO e com o tipo "Other/Uknown"
* Após criada, vamos nas configurações da máquina virtual:
	* Na aba "System" e "Motherboard" verificamos que a boot order apresenta "Floppy" no início da lista.
	* Na aba "Storage" e "Storage Devices" vamos na parte inferior do menu e selecionamos "Add controller"
	* Selecionamos "Floppy I82078"
	* Selecionamos nosso controller recém criado e selecionamos "Add floppy drive."
	* Selecionamos o nosso boot.img
* Finalmente podemos iniciar a máquina virtual e verificar o "Hello world!" imprimido na tela.

### 1.1 - Solução de problemas do tópico anterior

* Caso o comando VBoxManage tenha algum erro na criação do arquivo de imagem, verifique o tamanho do boot.bin, ele deve ter algum múltiplo de 512 bytes de tamanho.
	* A razão para o requisito específico de tamanho de arquivo, especialmente no contexto de setores de inicialização e imagens de disco, está na forma como os dispositivos de armazenamento e sistemas de arquivos funcionam.
	* Na maioria dos sistemas de armazenamento, os dados são organizados em unidades de tamanho fixo chamadas setores. Um setor normalmente tem 512 bytes de tamanho. Ao criar um setor de inicialização ou uma imagem de disco, ele precisa respeitar o tamanho do setor para compatibilidade com a mídia de armazenamento e o sistema de arquivos.
	* Quando um computador é inicializado, o firmware BIOS ou UEFI lê o setor de inicialização do dispositivo inicializável (como um disquete, disco rígido ou unidade USB). Ele espera que o setor de inicialização tenha exatamente um setor de tamanho (512 bytes para a maioria dos sistemas). Se o setor de inicialização for menor ou maior que um setor, ele poderá não ser reconhecido ou executado corretamente.
* Um truque bem simples para atingir um tamanho específico de arquivo é criar um outro com o tamanho que falta. Isso pode ser feito no command prompt do windows da seguinte forma:

```sh
fsutil file createnew [LOCAL DO ARQUIVO] [TAMANHO]
```

* Onde [LOCAL DO ARQUIVO] é o destino do arquivo, por exemplo "C:\Users\[SEU NOME]\Desktop\NovoArquivo"
* E [TAMANHO] é o tamnho em bytes do arquivo, por exemplo 512
* Ou seja, se o boot.bin tem 521 bytes, o próximo múltiplo de 512 é 1024, ou seja, precisamos criar um arquivo de exatamente 503 bytes tal que 1024 - 521 = 503.
* Após isso utilizamos o seguinte comando para juntar os dois arquivos:

```sh
copy /b boot.bin + NovoArquivo boot_incrementado.bin
```

* Onde "boot.bin" e "NovoArquivo" são os arquivos origem e "boot_incrementado.bin" será o resultante. Certifique-se que os arquivos e o seu command prompt estão na mesma pasta!

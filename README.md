# SimpleOS

![Proverb](https://i.imgur.com/odnJTIh.jpeg)

> Se não queres que ninguém saiba, não o faças.
> 
> Provérbio chinês

## Objetivo
 Criar um sistema operacional simples para aprender e compreender melhor o funcionamento de um SO, seguindo o projeto do [KiddieOS.Community.](https://www.youtube.com/watch?v=Jws7BHrts6g&list=PLsoiO2Be-2z8BfsSkspJfDiuKeC9-LSca&index=1)


## Ferramentas Utilizadas

| Ferramenta | Link |
| ------ | ------ |
| NASM | https://www.nasm.us/ |
| Rufus | https://rufus.ie/en/ |
| FergoRaw | https://www.fergonez.net/softwares/fraw |
| VirtualBox | https://www.virtualbox.org/wiki/Download_Old_Builds_5_2 |

## Variáveis do Sistema

Primeiramente, devemos configurar o ambiente de desenvolvimento do computador, estabelecendo as variáveis `PATH` necessária para os softwares descritos acima.

1. Pesquise por variáveis de sistema no menu iniciar.

![Pesquisa](https://i.imgur.com/ZmfcWaA.png)

2. Selecione variáveis de ambiente.

![SystemProps](https://i.imgur.com/RW6MQ99.png)

3. Selecione a variável `PATH` e clique no botão de editar.

![EnviromentVars](https://i.imgur.com/F5EIOTf.png)

4. Clique no botão para criar uma nova variável e utilize as pastas dos executáveis `NASM` e `VirtualBox`.

![EnviromentVars](https://i.imgur.com/8MFT2Ju.png)

## Compilação dos arquivos .asm com o NASM

Agora vamos criar um arquivo assembler.bat para automatizar o processo de geração de arquivos binários realizada pelo compilador `NASM`.

		ECHO  OFF
		cls

		echo Montando o arquivo bootloader...
		nasm.exe -f bin bootloader.asm -o Binary/bootloader.bin
		echo Montando o arquivo kernel...
		nasm.exe -f bin kernel.asm -o Binary/kernel.bin
		echo Montando o arquivo window...
		nasm.exe -f bin window.asm -o Binary/window.bin
		pause

Esses arquivos correspondem aos códigos assembly no repositório.

![TerminalNASM](https://i.imgur.com/1x7GbCy.png)

![binFiles](https://i.imgur.com/bdZfERw.png)

## Criação de um arquivo .img com o FergoRaw

Com os arquivos binários, podemos estabelecer a estrutura do disco de nossa imagem através do `FergoRaw`.

Caso o `FergoRaw` tenha algum tipo de erro em sua execução, procure baixar e instalar `Ascentive Library Files`.

![FergoRaw](https://i.imgur.com/nPyMDgr.png)

Nota-se que todos os componentes estão no cabeçote 0, cilindro 0, o `bootloader` sempre deve estar no primeiro setor, seguido pelo arquivo `kernel` nos setores 2 e 3, e finalmente pelo `window` nos setores 4 e 5.

"File to add to the raw image" significa justamente os arquivos que vamos adicionar no disco, `bootloader.bin`, `kernel.bin` e `window.bin`.

Output file significa o local onde a imagem gerada será salva.

## Criando uma imagem virtual com um pen drive USB físico

1. Insira um pen drive USB em uma das portas do seu computador.

2. Pesquise por criar e formatar partições do sistema no menu iniciar.

![Disco](https://i.imgur.com/yAwrsot.png)

3. Verifique qual o número do disco do seu pen drive, procure por um disco removível com a mesma capacidade dele.

![Partições](https://i.imgur.com/KYehWrg.png)

4. Com esse conhecimento em mãos, podemos criar um disco virtual com o seguinte comando:

		VBoxManage internalcommands createrawvmdk -filename "%USERPROFILE%"\.VirtualBox\usb.vmdk -rawdisk \\.\PhysicalDrive [numero]

5. Feito isso, você terá um arquivo `usb.vmdk` em `C:\Users\\[Seu usuário]\\.VirtualBox`

## Formatando o pen drive com a imagem do Sistema Operacional

Com o pen drive conectado e a imagem gerada pelo `FergoRaw`, agora podemos utilizar o `Rufus` para formatar o drive com ela.

![Rufus](https://i.imgur.com/BdoAsp9.png)

1. Em device, selecione o seu pen drive.

2. Em select, busque o arquivo imagem gerado pelo `FergoRaw`.

3. Quando o status estiver pronto, clique em start.

## Configurando Oracle VirtualBox

1. Abra o `VirtualBox`.

2. Aperte `Ctrl + N` em seu teclado para criar uma nova máquina virtual.

3. Escolha um nome e pasta para sua máquina, deixe sem imagem ISO, tipo escolha como outro e versão outro/desconhecido.

![NewVM](https://i.imgur.com/jwyt7Rr.png)

4. Deixe os valores padrões nos próximos passos.

5. Quando chegar na parte de disco virutal, selecione "use an existing virtual hard disk file", clique no ícone da pasta ao lado e utilize o `usb.vmdk` gerado anteriormente.

![VirtualUSB](https://i.imgur.com/cNFsP1H.png)

6. Selecione a sua máquina, clique em start e aguarde a máquina inicializar.

![SO](https://i.imgur.com/4bDxcaT.png)

## Observações finais de sabedoria

![ConfuciusQuote](https://i.imgur.com/YpsM4hx.jpeg)

> A experiência é uma lanterna dependurada nas costas que apenas ilumina o caminho já percorrido.
> 
> Confúcio

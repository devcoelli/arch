Entrando em um shell temporário
Suponha que queremos um ambiente onde cowsay e lolcat estão disponíveis. A maneira mais simples possível de fazer isso é através do comando:nix-shell -p

$ nix-shell -p cowsay lolcat
Este comando funciona, mas há uma série de desvantagens:

Você tem que digitar toda vez que você entra na concha.-p cowsay lolcat

Ele não permite (ergonomicamente) qualquer personalização adicional do seu ambiente shell.

Uma solução melhor é criar nosso ambiente shell a partir de um shell.nix arquivo.

Um básico shell.nix arquivo
Crie um arquivo chamado shell.nix com estes conteúdos:

let
  nixpkgs = fetchTarball "https://github.com/NixOS/nixpkgs/tarball/nixos-24.05";
  pkgs = import nixpkgs { config = {}; overlays = []; };
in

pkgs.mkShellNoCC {
  packages = with pkgs; [
    cowsay
    lolcat
  ];
}
Entre no ambiente executando nix-shell no mesmo diretório que shell.nix:

Nota

A primeira invocação de nix-shell neste arquivo pode demorar um pouco para baixar todas as dependências.

nix-shell
cowsay hello | lolcat
nix-shell por padrão procura um arquivo chamado shell.nix no diretório atual e constrói um ambiente shell a partir da expressão Nix neste arquivo. Pacotes definidos no packages o atributo estará disponível em $PATH.

Variáveis de ambiente
Você pode querer exportar automaticamente certas variáveis de ambiente quando você entra em um ambiente de shell.

Conjunto GREETING então ele pode ser usado no ambiente shell:

 let
   nixpkgs = fetchTarball "https://github.com/NixOS/nixpkgs/tarball/nixos-24.05";
   pkgs = import nixpkgs { config = {}; overlays = []; };
 in

 pkgs.mkShellNoCC {
   packages = with pkgs; [
     cowsay
     lolcat
   ];

+  GREETING = "Hello, Nix!";
 }
Qualquer nome de atributo passado para mkShellNoCC isso não é reservado de outra forma e tem um valor que pode ser coagido a uma string vai acabar como uma variável de ambiente.

Experimente! Saia do shell digitando exit ou pressionando Ctrl+D, então comece novamente com nix-shell.

echo $GREETING
Aviso

Algumas variáveis são protegidas de serem definidas como descrito acima.

Por exemplo, o formato de prompt de shell para a maioria dos shells é definido pelo PS1 variável de ambiente, mas nix-shell já define isso por padrão, e irá ignorar um PS1 atributo definido no argumento.

Se você precisar substituir essas variáveis de ambiente protegidas, use o shellHook atributo conforme descrito na próxima seção.

Comandos de inicialização
Você pode querer executar alguns comandos antes de entrar no ambiente shell. Esses comandos podem ser colocados no shellHook atributo fornecido para mkShellNoCC.

Conjunto shellHook para saída de uma saudação colorida:

 let
   nixpkgs = fetchTarball "https://github.com/NixOS/nixpkgs/tarball/nixos-24.05";
   pkgs = import nixpkgs { config = {}; overlays = []; };
 in

 pkgs.mkShellNoCC {
   packages = with pkgs; [
     cowsay
     lolcat
   ];

   GREETING = "Hello, Nix!";
+
+  shellHook = ''
+    echo $GREETING | cowsay | lolcat
+  '';
 }
Tente novamente! Saia do shell digitando exit ou pressionando Ctrl+D, então comece novamente com nix-shell para observar o efeito.

Referências
mkShelldocumentação

Nixpkgs funções e utilitários shell documentação

nix-shelldocumentação

Próximos passos
Noções básicas de linguagem Nix

Ativação automática do ambiente com direnv

Dependências no shell de desenvolvimento

Gerenciando automaticamente fontes remotas com npins

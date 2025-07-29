# 💻 deb-uninstaller

Criação de Script Zsh via Terminal

  1. Interface simples via atalho { tipo CTRL + K }
  2.  Exibição de:

   - Nome do app;
   - Caminho de instalação;
   - Dependências instaladas junto com ele;
   - Ícones e arquivos associados { para remover };

  3. Execução automática de:

         sudo apt auroremove --purge
         sudo apt clean

# 1️⃣ Etapa I : Visão geral do que o script fará

  1. Listar pacotes .deb instalados;
  2. O usuário escolhe qual remover { usando fzf ou interface simples };
  3. Mostrar detalhes:

     - Caminho dos arquivos
     - Dependências
     - Ícones e atalhos
    
  4. Confirmar e remover o app com apt remove --purge
  5. Executar autoremove e clean

# 📑 Pré - requisitos

*Este script usará o utilitário fzf para seleção interativa { atalho tipo "CTRL + K" }
*Se não estiver instalado, faça:

    sudo apt install fzf

*Script : deb-uninstaller.zsh

    #!/bin/zsh

    # Verifica se fzf está instalado
    if ! command -v fzf >/dev/null; then
      echo " 'fzf' não está instalado. Instale com: sudo apt install fzf"
      exit 1
    fi

    echo " Carregando lista de pacotes instalados..."

    # Seleciona pacotes usando fzf (Ctrl+K estilo)
    pkg=$(dpkg-query -W -f='${binary:Package}\n' | sort | fzf --prompt=" Selection"

    if [[ -z "$pkg" ]]; then
      echo " Nenhum pacote selecionado. Cancelando".
      exit 1
    fi

    echo "\n Pacote selecionado: $pkg"

    # Mostrar localização de arquivos instalados
    echo "\n Arquivos instalados:"
    dpkg -L "$pkg"

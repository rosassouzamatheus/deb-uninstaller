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

  1. Listar pacotes .deb instalados
  2. O usuário escolhe qual remover { usando fzf ou interface simples }
  3. Mostrar detalhes:

     - Caminho dos arquivos;
     - Dependências;
     - Ícones e atalhos;
    
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
      echo "❌️ 'fzf' não está instalado. Instale com: sudo apt install fzf"
      exit 1
    fi

    echo "🔍 Carregando lista de pacotes instalados..."

    # Seleciona pacotes usando fzf (Ctrl+K estilo)
    pkg=$(dpkg-query -W -f='${binary:Package}\n' | sort | fzf --prompt="📦 Selection"

    if [[ -z "$pkg" ]]; then
      echo "❌️ Nenhum pacote selecionado. Cancelando".
      exit 1
    fi

    echo "\n📦 Pacote selecionado: $pkg"

    # Mostrar localização de arquivos instalados
    echo "\n🗂 Arquivos instalados:"
    dpkg -L "$pkg"

    # Dependências diretas
    echo "\n Dependências diretas:"
    apt-cache depends "$pkg" | grep "Depends" | sed 's/.*Depends:\s*//'

    # Localizando ícones e atalhos
    echo"\n Atalhos e ícones encontrados:"
    icons=($(find ~/.local/share/applications /usr/share/applications -iname "*pkg*"
    printf '%s\n' $icons

    # Localizando pastas de configuração
    echo "\n Pastas de configuração sugeridas para remoção:"
    configs=(
     ~/.config/$pkg
     ~/.local/share/$pkg
     ~/.cache/$pkg
     ~/.config/${pkg%-*} # tentativa mais genérica
     ~/.local/share/${pkg%-*}
     ~/.cache/${pkg%-*}
    )
    for dir in $configs; do
     [[ -d "$dir"]] && echo "$dir"
    done

    # Confirmação final
    echo "\n Deseja remover o pacote, atalhos, configs e limpar o sistema? [s/N]
    read -k 1 confirm
    echo ""

    if [[ "$confirm" == [sS] ]]; then
      echo "\n Removendo $pkg com apt..."
      sudo apt remove --purge -y "$pkg"

      echo "\n Removendo atalhos (.desktop)..."
      for icon in $icons; do
       rm -v "$icon"
      done

      echo "\n Removendo configurações do usuário..."
      for dir in $configs; do
       [[ -d "$dir" ]] && rm -rfv "$dir"
      done

      echo "\n Limpando pacotes órfãos..."
      sudo apt autoremove --purge -y
      sudo apt clean

       echo "\ $pkg removido completamente!"
      else
       echo " Cancelando."
      fi

# 📥 Como utilizar

  1. Salve como deb-uninstaller.zsh
  2. Dê permissão de execução:

    chmod +x deb-uninstaller.zsh

  3. Execute:

    ./deb-uninstaller.zsh

# 📰 Extras (opcionais)

*Quer integrar ao terminal com um atalho ou comando permanente como removerapp?*
*Basta adicionar isso ao seu ~/.zshrc:

    alias removerapp='~/caminho/para/deb-uninstaller.zsh'

*Depois:

    source ~/.zshrc

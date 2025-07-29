# deb-uninstaller 💻

Criação de Script Zsh via Terminal

- Interface simples via atalho { tipo CTRL + K }
- Exibição de:

  1. Nome do app;
  2. Caminho de instalação;
  3. Dependências instaladas junto com ele;
  4. Ícones e arquivos associados { para remover };

- Execução automática de:

# Etapa I : Visão geral do que o script fará

  1. Listar pacotes .deb instalados;
  2. O usuário escolhe qual remover { usando fzf ou interface simples };
  3. Mostrar detalhes:

     - Caminho dos arquivos
     - Dependências
     - Ícones e atalhos
    
  4. Confirmar e remover o app com apt remove --purge
  5. Executar autoremove e clean

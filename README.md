# rofi-pass

Seletor de senhas do [`pass`](https://www.passwordstore.org/) no rofi para **GNOME/Wayland** (Ubuntu 24.04).
Escolhe uma entrada, copia a senha (1ª linha) e cola no app em foco.

## Uso

| Tecla | Ação |
|---|---|
| Enter | cola a senha no app em foco |
| Alt+C | só copia |
| Alt+N | nova senha (nome + senha); o texto digitado na busca vira o nome |
| Alt+R | renomeia a entrada |
| Alt+D | apaga, só depois de digitar o nome exato |
| Esc | fecha |

Busca difusa; as entradas usadas recentemente aparecem primeiro (só os nomes ficam em `~/.cache/rofi-pass/history`).

## Instalação

Dependências: `rofi`, `pass`, `wl-clipboard`, `xdotool`, `wmctrl`, `python3-gi`, `libnotify-bin`.

```sh
ln -s "$PWD/rofi-pass" ~/.local/bin/rofi-pass
```

Atalho do GNOME (Ctrl+Alt+P): comando `~/.local/bin/rofi-pass`.
O tema `rofi-pass.rasi` fica na mesma pasta do script.

## Como funciona no Wayland

- O rofi 1.7.5 é X11 e roda via XWayland, no monitor `primary`. O GNOME não tem `layer-shell`, então não há overlay nativo.
- Abre com `-normal-window`: sem isso o Mutter não dá foco de teclado ao rofi.
- A função `center` (em segundo plano) centraliza a janela, tira ela do Alt+Tab/barra (`skip_taskbar,skip_pager`) e **fecha o rofi ao perder o foco**.
- Cópia com `wl-copy` no clipboard **e** na seleção primária; as duas são limpas após 30 s, só se ainda tiverem a senha.
- Colagem: **Shift+Insert** injetado pelo D-Bus `org.gnome.Mutter.RemoteDesktop` (sem diálogo; o GNOME mostra o ícone de controle remoto durante a sessão). Shift+Insert porque o Wayland não revela a classe da janela em foco: o kitty cola a primária, os demais apps colam o clipboard. Se a injeção falhar, a senha fica copiada e vem uma notificação.
- A senha mestre é a passphrase GPG, em cache no `gpg-agent` (padrão: 10 min sem uso, 2 h no máximo).

## Problemas

- Rofi travou a tela: `pkill -x rofi`.
- Não colou: a senha continua no clipboard, cole com Ctrl+V.

## Licença

[MIT](LICENSE)

# Paprium H700 — PortMaster / muOS / KNULLI

Integração independente de **Paprium por emulação**, com frontend SDL2 e Genesis Plus GX PUNCHiUM, para aparelhos Allwinner H700 ARM64 com 1 GB de RAM. Mantido por **VVmImo**.

**Não é um port nativo do jogo, um core de RetroArch ou um projeto oficial. Não inclui ROM nem músicas.**

## Estado — alpha 0.5.1

| Firmware | Estado | Pacote |
| --- | --- | --- |
| muOS | Usado num RG40XX H com muOS 2601.0; utilizador confirmou melhoria de áudio. Validação de todo o jogo pendente. | `Paprium-H700-muOS-alpha-0.5.1.muxupd` |
| muOS, extração manual | Mesmo motor; permite escolher explicitamente o cartão de instalação. | `paprium-h700-alpha-0.5.1.zip` |
| KNULLI | **Experimental, ainda não testado num aparelho com KNULLI.** | `Paprium-H700-KNULLI-alpha-0.5.1-EXPERIMENTAL.tar.gz` |

Descarrega os pacotes e as fontes correspondentes em [Releases](https://github.com/VVmImo/paprium-h700-portmaster/releases). Não é anunciado como disponível no catálogo oficial PortMaster.

## Instalar no muOS

1. Instala/atualiza o PortMaster do teu firmware.
2. Copia o `.muxupd` para `ARCHIVE` num cartão e instala pelo **Archive Manager**.
3. O destino é `/mnt/union/ROMS/Ports`, sem obrigar a SD2. Localiza a pasta física instalada em SD1 (`/mnt/mmc/ROMS/Ports`) ou SD2 (`/mnt/sdcard/ROMS/Ports`). A posição do arquivo instalador não escolhe o ramo de escrita do unionfs.
4. Copia a ROM descomprimida para `paprium/gamedata/paprium.bin` e os **52 MP3** para `paprium/gamedata/paprium/`, dentro dessa instalação.
5. Abre o **ficheiro Paprium.sh** em Ports e espera a conversão automática dos WAV em falta.

Para escolher explicitamente SD1 ou SD2, extrai o ZIP manual para `ROMS/Ports` do cartão escolhido. Não renomeies ZIP para `.muxupd`. Evita duas instalações com o mesmo nome nos dois cartões.

**[Manual muOS detalhado](MANUAL-DE-INSTALACAO.txt)** — caminhos PC/SFTP, formatos, ROM de referência, 52 nomes de músicas, teste limpo e diagnóstico.

## Instalar no KNULLI — experimental

1. Instala/atualiza o PortMaster. A [documentação KNULLI](https://knulli.org/systems/portmaster/) descreve a instalação no menu Device Settings.
2. Extrai o TAR.GZ e copia `Instalar Paprium.sh` e a pasta completa `paprium-knulli` para `/userdata/roms/ports/` (`SHARE/roms/ports` pela rede).
3. Atualiza a lista de jogos, abre **Instalar Paprium** em Ports e espera a conclusão. Atualiza a lista novamente.
4. Copia a ROM para `/userdata/roms/ports/paprium/gamedata/paprium.bin` e os 52 MP3 para `/userdata/roms/ports/paprium/gamedata/paprium/`.
5. Abre **Paprium** em Ports. Vídeo, áudio, controlos, conversão integral e regresso ao menu ainda precisam de teste real no KNULLI.

**[Manual KNULLI detalhado](MANUAL-KNULLI.txt)**. O pacote não modifica `/usr/bin`, `custom.sh`, `knulli.conf` ou o catálogo global do EmulationStation.

## Dados fornecidos pelo utilizador

A ROM de referência tem **8 388 608 bytes**, identificador `T-574120-00` e SHA-256:

```text
f9192ac55b915ac65765688bd4944c98d4ebe2ea994e6d312e2e6e52f7e79d09
```

Outra revisão exige teste. Mudar o nome ou o cabeçalho não torna uma ROM compatível. Os nomes exatos das músicas estão nos manuais e em [TRACKS.txt](TRACKS.txt).

O conversor incluído cria WAV PCM estéreo de 16 bits/48 kHz a partir dos MP3, apenas quando faltam WAV válidos. Não descarrega dados. Os MP3 são conservados; faixas já convertidas são aproveitadas. Reserva **pelo menos 3 GB livres depois de copiar os dados** para o conjunto de referência e mantém alimentação durante a primeira conversão. Um WAV inválido provoca erro sem o substituir.

Não incluas ROM, MP3/WAV do jogo, BIOS, capas ou gravações pessoais em redistribuições. Os pequenos MP3/WAV em `tests/converter-fixtures` nas fontes são sinais sintéticos criados para os testes e não contêm áudio do jogo.

## Controlos

| Entrada física | Mega Drive / função |
| --- | --- |
| Direcional / stick esquerdo | Direções |
| Botão de face esquerdo / inferior / direito / superior | A / B / C / X |
| L1 / R1 | Y / Z |
| Start / Select | Start / Mode |
| **Select + Start** | Sair normalmente |
| **Select + R1** | Reset suave; usado no minijogo inicial |

O mapeamento usa SDL/PortMaster. Dois comandos são suportados no código; teste físico de dois jogadores pendente.

## Gravações, áudio e limitações

A SRAM fica em `paprium/saves/paprium.srm` (64 KiB). Alterações são verificadas a cada cinco segundos e ao sair. Isto conserva o progresso registado pelo jogo, não um savestate de qualquer ponto de uma missão. Fecha o jogo antes de copiar/restaurar SRAM.

A 0.5.1 corrige amplificação indevida entre vozes dos efeitos e aplica um filtro de reconstrução de 6 kHz apenas aos efeitos/eco. A música mantém o tratamento da 0.5, com rejeição de DC a 5 Hz e uma reserva inicial de áudio de cerca de 50 ms. Não precisas de voltar a converter os WAV. Os dados originais de efeitos de 4/8 bits continuam a limitar a fidelidade.

Opções de comparação técnica: `PAPRIUM_SFX_FILTER=0` desliga o filtro dos efeitos; `PAPRIUM_LEGACY_AUDIO_FILTER=1` liga o filtro global antigo, desligado por defeito. O port utiliza uma implementação experimental do hardware do cartucho: não se garante reprodução exata, ausência de bloqueios ou sincronização perfeita em todo o jogo.

`paprium/log.txt` e `paprium/conf/conversion-status.txt` ajudam no diagnóstico. Guarda o log antes de reabrir o jogo. Ao reportar falhas, indica modelo, firmware, versão do pacote, SHA-256 da ROM e cenário; não envies os próprios dados do jogo.

## Fontes, compilação e relink

O [arquivo de fontes completo](paprium-h700-source-alpha-0.5.1.tar.gz) está disponível neste repositório e nas Releases. Inclui frontend, núcleo modificado, dependências, scripts, testes e licenças. O [kit de relink](paprium-h700-relink-alpha-0.5.1.tar.gz) acompanha os executáveis.

Cada pacote inclui **`source.tar.gz` com as fontes correspondentes completas** e **`relink.tar.gz` com os objetos da compilação e instruções para relink**, incluindo substituição dos componentes LGPL. As Releases disponibilizam também uma cópia independente dessas fontes.

Em Linux, extrai `source.tar.gz`, entra na pasta `paprium-h700-source` e executa:

```sh
bash scripts/setup-toolchain.sh
bash scripts/build.sh
```

São necessários Python 3, CMake, curl e ferramentas de compilação usuais. O script obtém e verifica o toolchain Bootlin ARM64 2020.08-1. Fontes SDL2 e zlib estão incluídas; não se distribui a SDL2 de teste como biblioteca do aparelho. O programa usa a SDL2 do firmware. A arquitetura é ARMv8-A SIMD, com afinação Cortex-A53; requisito de símbolos GLIBC até 2.29.

Para testes sem aparelho: QEMU user-mode, ferramentas do sistema e `bash scripts/build-test-sdl.sh`, seguido de `bash scripts/test.sh` e `python3 scripts/test-converter.py`. Os testes usam ROM e áudio sintéticos. Resultados de QEMU não substituem validação no firmware real.

Para recompilar ou substituir bibliotecas LGPL, consulta `relink/README.txt` dentro de `relink.tar.gz`. As instruções e os objetos permitem relink sem depender de dados do jogo. Não atribuímos uma licença MIT/GPL genérica ao conjunto: cada componente conserva as suas condições.

## Créditos e licenças

- [Genesis Plus GX](https://github.com/libretro/Genesis-Plus-GX): Charles MacDonald, EkeEke e restantes autores; inclui restrições de uso e distribuição não comercial.
- [Genesis Plus GX PUNCHiUM](https://github.com/pav1388/Genesis-Plus-GX-PUNCHiUM), ramo `punchium`, commit `3849f3d3432df1d6320574e73695dd379ecef2b3`: base do suporte experimental a Paprium.
- [PAPwiiUM](https://github.com/Genesis64X/Genesis-Plus-G64X_PAPwiiUM): referência de adaptação para Wii; não é o executável Linux usado aqui.
- Nuked OPN2 (Nuke.YKT), blip_buf e filtros NTSC (Shay Green): LGPL-2.1 ou posterior, conforme cada ficheiro.
- SDL2, zlib, minimp3, dr_wav e stb_vorbis: autores e licenças próprios preservados nas fontes e em [THIRD-PARTY.txt](THIRD-PARTY.txt).
- Paprium e os seus dados pertencem aos respetivos titulares. O projeto não concede direitos sobre esses dados.

Consulta [LICENSE.txt](LICENSE.txt), os [avisos das bibliotecas](THIRD-PARTY.txt) e os avisos nos ficheiros. Os termos não comerciais do núcleo não substituem os direitos específicos dos componentes LGPL/permissivos. Modificação para uso próprio e engenharia inversa para depurar alterações nesses componentes LGPL são permitidas conforme os respetivos termos.

Projeto **não oficial**, sem afiliação ou aprovação por WaterMelon, Genesis Plus GX, PortMaster, muOS ou KNULLI. A presença no GitHub não constitui certificação de compatibilidade nem autorização sobre os dados do jogo.

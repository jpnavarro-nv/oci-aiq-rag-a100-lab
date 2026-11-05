# Configuração das releases NVIDIA

Os patches deste diretório ajustam os arquivos oficiais NVIDIA para executar AI-Q e RAG Blueprint localmente em uma instância com A100 de 80 GB.

| Patch | Commit-base | Arquivos modificados |
| --- | --- | --- |
| `rag-v2.6.2-oci-a100.patch` | `f20716d73ae69a544ad4a692f38d6178a64e6f36` | Quatro arquivos em `deploy/compose/`: NIMs, bancos, ingestão e RAG |
| `aiq-v2.2.1-oci-a100.patch` | `6ec77d91cc5f3ba790729bb8bdc8189a821a61d3` | `deploy/compose/docker-compose.yaml` e `configs/config_web_frag.yml` |

## Configurações aplicadas

- Perfil BF16 TP4 para o Nemotron Super 120B, com parsers de ferramentas e raciocínio.
- GPUs 0–3 para o LLM, 4 para embeddings, 5 para reordenação e 7 para extração.
- Caches separados por serviço e gravados com o UID do operador (`${USERID}:0`), portas do host em loopback e redes entre AI-Q e RAG.
- Inferência local em todos os papéis do workflow AI-Q e integração Foundational RAG.
- Credenciais por variáveis e persistência em volumes identificados pelo `LAB_ID` exclusivo da instalação. Projetos, contêineres e redes também usam esse identificador.
- Arquivos de configuração e documentos montados somente para leitura; os únicos bind mounts graváveis são os três caches com UID do operador.
- Seleção dos serviços RAG pelo perfil Compose `lab2`.

O código das aplicações, os agentes, as ferramentas e o fluxo de pesquisa continuam os das releases NVIDIA.

A etapa 19 do LAB2 oferece uma alternativa com Lightning 30B BF16 TP2. Ela carrega `config/compose.lightning.yaml` depois dos arquivos oficiais, com cache separado, sem alterar os patches-base ou os modelos de embeddings e extração. `config/lightning.env` seleciona o mesmo nome de modelo nos clientes RAG e AI-Q. O patch adicional `aiq-v2.2.1-lightning.patch`, aplicado depois do patch-base AI-Q, define `parallel_tool_calls: false` nos dois papéis originalmente Ultra, como nos papéis Lightning do upstream. Ele modifica apenas `configs/config_web_frag.yml` e deve ser revertido para retornar à configuração original do Super 120B.

## Aplicação e manutenção

Siga a [etapa 6 do roteiro](../docs/LAB2.md#6-aplicar-os-patches-de-configuração). Depois da aplicação, o Git mostra modificações intencionais nos seis arquivos de configuração dos submódulos; seus commits-base permanecem iguais.

Não aplique o mesmo patch duas vezes. Em caso de conflito, confira o commit e o diff local antes de continuar. Para mudar de release, pare os serviços, confira a reversão com `git apply --reverse --check`, reverta os patches e revise-os para a nova base. A reversão de configuração não apaga volumes ou caches.

Os comandos de inicialização usam `--pull missing --no-build`: baixam imagens oficiais ausentes, reutilizam imagens presentes e não compilam as aplicações. O NIM baixa os pesos e artefatos necessários quando o cache está vazio ou incompleto.

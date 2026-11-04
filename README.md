# NVIDIA AI-Q + RAG Blueprint na OCI

Laboratório em português para implantar um assistente de pesquisa com documentos, busca web e geração de relatórios em uma instância OCI com GPUs NVIDIA A100.

**Comece pelo [roteiro completo — Lab 2](docs/LAB2.md).** Ele apresenta os pré-requisitos, a instalação, os comandos, os resultados esperados e a solução de problemas.

## Componentes

| Componente | Configuração |
| --- | --- |
| AI-Q | 2.2.1 |
| RAG Blueprint | 2.6.2 |
| Modelo de linguagem | Nemotron 3 Super 120B, NIM 2.0.9, BF16 em quatro A100 |
| Embeddings e reordenação | NIMs locais |
| Documentos e pesquisa | NV-Ingest, Elasticsearch, SeaweedFS e Tavily |
| Sessões e tarefas | PostgreSQL |
| Implantação | Docker Compose, arquivos oficiais NVIDIA com patches locais de configuração |

O mapa utiliza sete GPUs de uma instância com oito A100 SXM4 de 80 GB. A inferência e os documentos permanecem no servidor; a pesquisa web utiliza Tavily e fontes externas.

## Organização

- [docs/LAB2.md](docs/LAB2.md): roteiro de instalação e uso.
- [patches/README.md](patches/README.md): configurações aplicadas às releases NVIDIA.
- `upstream/aiq` e `upstream/rag`: submódulos dos repositórios oficiais, nos commits de `versions.lock.json`.
- `config/lab.env.example`: exemplo de GPUs, perfil do modelo e portas.
- `config/secrets.env.example`: campos das credenciais, sem valores.
- `checks/llm-chat.json`: pergunta de teste do modelo local.

As configurações preenchidas, chaves, caches, documentos e relatórios locais ficam fora do Git. Siga as etapas do roteiro para criar esses arquivos no servidor.

Cada instalação recebe um `LAB_ID` próprio, usado nos nomes de projetos, contêineres, redes e volumes Docker. Os caches dos modelos ficam dentro do clone e são gravados com o UID do usuário. Para retomar uma instalação, preserve seu `config/lab.env`; para um teste limpo, siga o LAB2 desde um clone novo e libere as portas e GPUs ocupadas pela execução anterior.

## Material para apresentação

- [Slide com QR code](slides/Laboratorio_OCI_NVIDIA_QR.pptx).
- [QR code do laboratório](docs/images/lab2-qr.png).

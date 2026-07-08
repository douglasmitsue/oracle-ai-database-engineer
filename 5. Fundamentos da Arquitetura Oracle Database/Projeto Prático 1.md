#### Quando Mudar o Tamanho do Bloco faz sentido ?

No Oracle Database, mudar o tamanho do bloco não é algo que se faz como ajuste operacional simples. É uma decisão de arquitetura. O tamanho padrão do banco é definido na criação e não pode ser alterado depois. Se você quiser usar outro tamanho, precisa criar uma nova tablespace com block size diferente e configurar o buffer pool correspondente no SGA.  Em cenários mais radicais, só recriando o banco.

Isso faz sentido quando existe um padrão claro de acesso ou tipo de dado que se beneficia de outro tamanho de bloco. Blocos maiores, como 16K ou 32K, são úteis para tabelas com linhas grandes, varreduras completas frequentes ou workloads analíticos, pois reduzem  I/O lógico e aumentam throughput sequencial. Blocos menores , com 2K ou 4K, fazem mais sentido em workloads OLTP com muitas leituras aleatórias e linhas pequenas, pois reduzem contenção e desperdício de espaço.
Outro caso relevante é para objetos  específicos. Por exemplo, índices muito acessados podem  se beneficiar de blocos menores para reduzir latch contention, enquanto tabelas de data warehouse podem ir para blocos maiores em uma tablespace dedicada.
O critério é sempre o equilíbrio entre três fatores: padrão de acesso (aleatórios vs sequencial), tamanho médio das linhas e concorrência. Se não houver um problema claro de performance ou desperdício, o padrão default (geralmente 8k) constuma ser a melhor escolha.

#### Memória: SGA e PGA

No Oracle, os componentes abaixo fazem parte da SGA (System Global Area) e controlam como o banco usa memória para executar SQL, cachear dados e garantir durabilidade. Ajustá-los só faz sentido quando há sintomas claros de gargalo, normalmente em V$* views.

- **buffer cache**: É o cache onde ficam os blocos de dados lidos do disco. Quando uma query precisa de dados, o Oracle tenta primeiro aqui. Se não encontrar, ocorre um physical read. Faz sentido aumentar quando há muitos acessos a disco e baixa taxa de acerto no cache (buffer cache hit ratio baixo ou muitos db file sequential/scattered read). Isso é comum em sistemas com grande volume de leitura repetida. Reduzir raramente é necessário, exceto em ambientes com memória limitada.
- **shared pool**: Armazena


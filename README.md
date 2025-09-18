# Java 
# Java
## Como executar
public class ListaDeProcessos {
    private NoProcesso inicio;
    private NoProcesso fim;
    private int tamanho;

    public ListaDeProcessos() {
        this.inicio = null;
        this.fim = null;
        this.tamanho = 0;
    }

    public boolean estaVazia() {
        return inicio == null;
    }

    public int getTamanho() {
        return tamanho;
    }

    public void adicionarFinal(Processo processo) {
        NoProcesso novoNo = new NoProcesso(processo);

        if (estaVazia()) {
            inicio = novoNo;
            fim = novoNo;
        } else {
            fim.proximo = novoNo;
            novoNo.anterior = fim;
            fim = novoNo;
        }
        tamanho++;
    }

    public Processo removerInicio() {
        if (estaVazia()) {
            return null;
        }

        Processo processoRemovido = inicio.processo;

        if (inicio == fim) {
            inicio = null;
            fim = null;
        } else {
            inicio = inicio.proximo;
            inicio.anterior = null;
        }

        tamanho--;
        return processoRemovido;
    }

    public Processo visualizarInicio() {
        if (estaVazia()) {
            return null;
        }
        return inicio.processo;
    }

    public String listarProcessos() {
        if (estaVazia()) {
            return "Vazia";
        }

        StringBuilder sb = new StringBuilder();
        NoProcesso atual = inicio;
        int count = 0;

        while (atual != null) {
            sb.append(atual.processo.nome);
            if (atual.proximo != null) {
                sb.append(" → ");
            }
            atual = atual.proximo;
            count++;
 
        return false;
    }
}
public NoProcesso(Processo processo) {
        this.processo = processo;
        this.anterior = null;
        this.proximo = null;
    }
} 

                }
            }

            System.out.println("\n Todos os processos finalizados!");

        } catch (IOException e) {
            System.out.println(" Erro: " + e.getMessage());
        }
    }
}
import java.io.IOException;

public class Scheduler {
    private ListaDeProcessos lista_alta_prioridade;
    private ListaDeProcessos lista_media_prioridade;
    private ListaDeProcessos lista_baixa_prioridade;
    private ListaDeProcessos lista_bloqueados;
    private int contador_ciclos_alta_prioridade;
    private int cicloAtual;

    public Scheduler() {
        lista_alta_prioridade = new ListaDeProcessos();
        lista_media_prioridade = new ListaDeProcessos();
        lista_baixa_prioridade = new ListaDeProcessos();
        lista_bloqueados = new ListaDeProcessos();
        contador_ciclos_alta_prioridade = 0;
        cicloAtual = 0;
    }

    public void carregarProcessos(String arquivo) throws IOException {
        LeitorCSV leitor = new LeitorCSV(arquivo);
        Processo processo;

        while ((processo = leitor.lerProximoProcesso()) != null) {
            switch (processo.prioridade) {
                case 1:
                    lista_alta_prioridade.adicionarFinal(processo);
                    break;
                case 2:
                    lista_media_prioridade.adicionarFinal(processo);
                    break;
                case 3:
                    lista_baixa_prioridade.adicionarFinal(processo);
                    break;
            }
        }

        leitor.fechar();
        System.out.println(" Processos carregados: " +
                lista_alta_prioridade.getTamanho() + " alta, " +
                lista_media_prioridade.getTamanho() + " média, " +
                lista_baixa_prioridade.getTamanho() + " baixa");
    }

    public void executarCicloDeCPU() {
        cicloAtual++;
        System.out.println("\n=== CICLO " + cicloAtual + " ===");

        // 1. Desbloquear processo mais antigo
        desbloquearProcesso();

        // 2. Verificar regra de prevenção de inanição
        Processo processoExecutado = null;

        if (contador_ciclos_alta_prioridade >= 5) {
            processoExecutado = executarPrevencaoInanição();
            contador_ciclos_alta_prioridade = 0;
        }

        // 3. Execução padrão
        if (processoExecutado == null) {
            processoExecutado = executarPadrao();
        }

        // 4. Processar o processo
        if (processoExecutado != null) {
            processarProcesso(processoExecutado);
        } else {
            System.out.println(" Nenhum processo para executar");
        }

        // 5. Exibir estado atual
        exibirEstadoAtual();
    }

    private Processo executarPrevencaoInanição() {
        if (!lista_media_prioridade.estaVazia()) {
            Processo processo = lista_media_prioridade.removerInicio();
            System.out.println("⚡ Prevenção de Inanição: Executando " + processo.nome);
            return processo;
        } else if (!lista_baixa_prioridade.estaVazia()) {
            Processo processo = lista_baixa_prioridade.removerInicio();
            System.out.println("⚡ Prevenção de Inanição: Executando " + processo.nome);
            return processo;
        }
        return null;
    }

    private Processo executarPadrao() {
        if (!lista_alta_prioridade.estaVazia()) {
            contador_ciclos_alta_prioridade++;
            return lista_alta_prioridade.removerInicio();
        } else if (!lista_media_prioridade.estaVazia()) {
            return lista_media_prioridade.removerInicio();
        } else if (!lista_baixa_prioridade.estaVazia()) {
            return lista_baixa_prioridade.removerInicio();
        }
        return null;
    }

    private void desbloquearProcesso() {
        if (!lista_bloqueados.estaVazia()) {
            Processo processoDesbloqueado = lista_bloqueados.removerInicio();
            processoDesbloqueado.foiBloqueado = true;

            switch (processoDesbloqueado.prioridade) {
                case 1:
                    lista_alta_prioridade.adicionarFinal(processoDesbloqueado);
                    break;
                case 2:
                    lista_media_prioridade.adicionarFinal(processoDesbloqueado);
                    break;
                case 3:
                    lista_baixa_prioridade.adicionarFinal(processoDesbloqueado);
                    break;
            }

            System.out.println(" Desbloqueado: " + processoDesbloqueado.nome);
        }
    }

    private void processarProcesso(Processo processo) {
        System.out.println(" Executando: " + processo.nome);

        // Verificar bloqueio por recurso
        if ("DISCO".equalsIgnoreCase(processo.recurso_necessario) && !processo.foiBloqueado) {
            System.out.println(" Bloqueado (DISCO): " + processo.nome);
            lista_bloqueados.adicionarFinal(processo);
            return;
        }

        // Executar processo
        processo.ciclos_necessarios--;

        if (processo.ciclos_necessarios <= 0) {
            System.out.println(" Finalizado: " + processo.nome);
        } else {
            // Reinserir no final da lista
            switch (processo.prioridade) {
                case 1:
                    lista_alta_prioridade.adicionarFinal(processo);
                    break;
                case 2:
                    lista_media_prioridade.adicionarFinal(processo);
                    break;
                case 3:
                    lista_baixa_prioridade.adicionarFinal(processo);
                    break;
            }
            System.out.println(" Reinserido: " + processo.nome + " (" + processo.ciclos_necessarios + " ciclos)");
        }
    }

    private void exibirEstadoAtual() {
        System.out.println("\n ESTADO ATUAL:");
        System.out.println("Alta: " + lista_alta_prioridade.listarProcessos());
        System.out.println("Média: " + lista_media_prioridade.listarProcessos());
        System.out.println("Baixa: " + lista_baixa_prioridade.listarProcessos());
        System.out.println("Bloqueados: " + lista_bloqueados.listarProcessos());
        System.out.println("Contador Anti-Inanição: " + contador_ciclos_alta_prioridade + "/5");
    }

    public boolean haProcessosAtivos() {
        return !lista_alta_prioridade.estaVazia() ||
                !lista_media_prioridade.estaVazia() ||
                !lista_baixa_prioridade.estaVazia() ||
                !lista_bloqueados.estaVazia();
    }
}

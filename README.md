# Relógio com Calculadora e Pomodoro Mundial

Este é um projeto em Python utilizando a biblioteca **Tkinter** para criar uma interface gráfica interativa com múltiplas funcionalidades:

- **Relógio Mundial** com atualização de horário e informações meteorológicas.
- **Calculadora funcional** com suporte a operações básicas.
- **Cronômetro** para gerenciar atividades.
- **Lista de Tarefas** para organização pessoal.

## Funcionalidades Principais

### Relógio Mundial
- Exibe a hora local e o horário em diferentes cidades ao redor do mundo.
- Permite a seleção de cidades por meio de um menu suspenso.
- Exibe informações meteorológicas para a cidade selecionada (requer conexão com a internet e uma chave de API válida do OpenWeatherMap).
- Personalização de cores para o texto e o fundo.

### Calculadora
- Suporte a operações matemáticas básicas (+, -, *, /).
- Botão de apagar (`⌫`) para correção de entradas.

### Cronômetro
- Cronômetro com as funções Iniciar, Pausar e Resetar.
- Exibição de tempo decorrido em formato `hh:mm:ss`.

### Lista de Tarefas
- Adicione tarefas para gerenciar suas atividades.
- Remova tarefas concluídas ou indesejadas.
- Integração com o cronômetro para auxiliar no gerenciamento de tempo de cada tarefa.

## Requisitos

Certifique-se de ter as seguintes dependências instaladas no seu ambiente:

- **Python 3.7+**
- **Tkinter** (já incluído na maioria das distribuições do Python)
- **Requests**
- **Pytz**

Instale as dependências adicionais com:
```bash
pip install requests pytz
```

## Configuração

1. **Clonar o repositório:**
   ```bash
   git clone https://github.com/seu-usuario/nome-do-repositorio.git
   cd nome-do-repositorio
   ```

2. **Configurar a chave de API para o OpenWeatherMap:**
   - Obtenha uma chave de API gratuita em [OpenWeatherMap](https://openweathermap.org/).
   - Substitua a variável `self.api_key` no código pela sua chave de API.

3. **Executar o programa:**
   ```bash
   python nome_do_arquivo.py
   ```

## Uso

1. Execute o programa para abrir a interface.
2. Navegue entre as funcionalidades usando os botões de alternância na parte inferior da janela:
   - **Relógio:** Mostra a hora local, horário mundial e clima.
   - **Calculadora:** Permite cálculos básicos.
   - **Cronômetro:** Gerencia seu tempo.
   - **Tarefas:** Adiciona ou remove atividades.

## Capturas de Tela

*(Adicione imagens ou GIFs da interface aqui)*

## Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues ou enviar pull requests com melhorias, correções ou novas ideias.

## Licença

Este projeto é licenciado sob a [MIT License](LICENSE).

---

Se você gostou do projeto, não esqueça de deixar uma estrela ⭐ no repositório!


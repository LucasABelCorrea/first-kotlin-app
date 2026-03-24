# Check Point 01
#### Aluno: Lucas Almeida Bel Correa - RM: 558539
#### Turma: 3° SIR

## Descrição do projeto

O projeto é um aplicativo desenvolvido em Kotlin com o seguinte escopo:
- Tela de Login
- Tela de Menu
- Tela de Pedidos
- Tela de Perfil

As telas são simples, contando apenas com o título de cada tela, botões de navegação entre cada tela e cores para identificação.

## Objetivo da prova

Aplicar as modificações realizadas no código, envolvendo implementação e definição de parâmetros obrigatórios e opcionais, a fim de demonstrar a capacidade do aluno de ampliar um código previamente construído utilizando documentação e histórico de commits.

## Documentação de cada evolução

### 1. Passagem de parâmetros obrigatórios na tela de perfil

Para tornar a navegação até a tela de perfil dependente de um parâmetro obrigatório, foi necessário definir esse parâmetro diretamente na rota. No arquivo `MainActivity`, a rota foi configurada como:

```kotlin
composable(route = "perfil/{nome}") {
    val nome: String? = it.arguments?.getString("nome", "Usuário Genérico")
    PerfilScreen(modifier = Modifier.padding(innerPadding), navController, nome!!)
}
```

O trecho `/{nome}` indica que a rota exige um argumento chamado **"nome"**. Isso significa que, ao navegar para essa tela, é obrigatório fornecer um valor para esse parâmetro.

Dentro do bloco da `composable`, é criada a variável:

```kotlin
val nome: String?
```

Ela é do tipo `String` nullable (ou seja, pode ser nula), o que é indicado pelo uso do `?`.

Para recuperar o valor passado na navegação, utilizamos:

```kotlin
it.arguments?.getString("nome", "Usuário Genérico")
```

- `it.arguments` acessa os argumentos enviados para a rota.
- O operador `?.` garante uma chamada segura (evita erro caso `arguments` seja null).
- `getString("nome", "Usuário Genérico")` tenta obter o valor do parâmetro `"nome"` e define `"Usuário Genérico"` como valor padrão.

Apesar da existência desse valor padrão, ele funciona apenas como **fallback** para evitar falhas na aplicação. Como o parâmetro é obrigatório na rota (`perfil/{nome}`), espera-se que ele sempre seja informado corretamente.

Por fim, ao chamar a tela de perfil, é necessário incluir a variável `nome` como parâmetro:

```kotlin
nome!!
```

O operador `!!` força o uso do valor, assumindo que ele **não é nulo**. Caso seja nulo, a aplicação irá lançar uma exceção. Por isso, é importante garantir que o parâmetro seja sempre passado corretamente durante a navegação.

Após definir a rota com parâmetro obrigatório para a tela de perfil, foi necessário ajustar a navegação na tela MenuScreen. Essa é a tela onde está localizado os botões responsáveis por redirecionar o usuário para as outras telas.

Para que a navegação funcione corretamente, é preciso passar um valor para o parâmetro `"nome"` diretamente na rota:

```kotlin
onClick = { navController.navigate("perfil/Fulano de Tal") }
```

Por fim, na implementação da `PerfilScreen`, foi adicionado o parâmetro `nome` na assinatura da função. Esse parâmetro é do tipo `String` e será responsável por receber o valor enviado durante a navegação.

```kotlin
fun PerfilScreen(
    modifier: Modifier = Modifier,
    navController: NavController,
    nome: String
)
```

Dentro do componente `Text`, foi realizada a concatenação do valor recebido com a string `"PERFIL - "` utilizando interpolação de string (`$nome`).

Dessa forma, quando o usuário clicar no botão na `MenuScreen` e for redirecionado para a `PerfilScreen`, o valor passado na rota (por exemplo, `"Fulano de Tal"`) será recebido e exibido na tela como: 
```text
PERFIL - Fulano de Tal
```

```kotlin
        Text(
            text = "PERFIL - $nome",
            fontSize = 24.sp,
            fontWeight = FontWeight.Bold,
            color = Color.White
        )
    }
}
```

Como a rota foi definida como `perfil/{nome}`, qualquer valor informado após `perfil/` será interpretado como o parâmetro `"nome"`.

É importante garantir que esse valor seja sempre informado, pois a ausência dele pode causar falhas na navegação ou até mesmo erros na aplicação.

### 2. Passagem de parâmetros opcionais na tela de Pedidos

Para implementar a passagem de parâmetros opcionais, foi definida uma rota com query parameter na navegação para a tela de pedidos. Nesse caso, foi utilizado o padrão `?cliente={cliente}`:

```kotlin
composable(
    route = "pedidos?cliente={cliente}",
    arguments = listOf(navArgument("cliente") {
        defaultValue = "Cliente Genérico"
    })
) {
    PedidosScreen(
        modifier = Modifier.padding(innerPadding),
        navController,
        it.arguments?.getString("cliente")
    )
}
```

O trecho `?cliente={cliente}` indica que o parâmetro `"cliente"` é **opcional**, ou seja, a navegação pode ocorrer mesmo sem que ele seja informado.

Em seguida, foi definida uma lista de argumentos com `navArgument`, onde foi atribuído um valor padrão:

```kotlin
defaultValue = "Cliente Genérico"
```

Isso garante que, caso nenhum valor seja passado na rota, o sistema utilizará automaticamente `"Cliente Genérico"` como valor padrão, evitando erros ou valores nulos inesperados.

Na chamada da `PedidosScreen`, o argumento é recuperado da seguinte forma:

```kotlin
it.arguments?.getString("cliente")
```

Diferente do que foi feito na `PerfilScreen`, aqui o valor é tratado como **opcional**, sem o uso do operador `!!`. Isso significa que a tela está preparada para funcionar mesmo que o parâmetro não seja informado.

Na implementação da `PedidosScreen`, foi adicionado o parâmetro `cliente` na assinatura da função. Diferente do caso da `PerfilScreen`, esse parâmetro é do tipo `String?`, ou seja, **opcional (nullable)**.

```kotlin
fun PedidosScreen(modifier: Modifier = Modifier, navController: NavController, cliente: String?) {
    Box(
        modifier
            .fillMaxSize()
            .background(Color(0xFF91FA98))
            .padding(32.dp)
    ) {
        Text(
            text = "PEDIDOS - $cliente",
            fontSize = 24.sp,
            color = Color.White
        )
```

Dentro do componente `Text`, foi utilizada a interpolação de string (`$cliente`) para concatenar o valor recebido com o texto `"PEDIDOS - "`.

Como o parâmetro é opcional, existem dois cenários possíveis:

- Caso um valor seja passado na navegação, ele será exibido normalmente na tela.
- Caso nenhum valor seja informado, será utilizado o valor padrão definido na rota (`"Cliente Genérico"`).

É importante observar que, se não houvesse um valor padrão definido e nenhum argumento fosse passado, o valor de `cliente` seria `null`. Nesse cenário, o texto exibido seria:

```text
PEDIDOS - null
```

### 3. Inserindo valor ao parâmetro opcional na tela de Pedidos

Para enviar um argumento para a rota opcional definida em `PedidosScreen`, foi necessário realizar a chamada de navegação a partir da `MenuScreen`, passando o parâmetro diretamente na URL da rota.

```kotlin
onClick = { navController.navigate("pedidos?cliente=Cliente XPTO") }
```

Nesse caso, o valor `"Cliente XPTO"` está sendo enviado como argumento para o parâmetro `"cliente"`.

Como a rota foi definida como `pedidos?cliente={cliente}`, qualquer valor informado após `cliente=` será interpretado e recebido pela `PedidosScreen`.

Dessa forma, ao clicar no botão, a tela de pedidos receberá o valor `"Cliente XPTO"` e irá utilizá-lo na interface. Como há uma concatenação no componente `Text`, o resultado exibido será:

```text
PEDIDOS - Cliente XPTO
```

Caso nenhum valor fosse informado na navegação, a tela utilizaria o valor padrão definido anteriormente (`"Cliente Genérico"`).

### 4. Passagem de múltiplos parâmetros

Para implementar a passagem de múltiplos parâmetros, foi necessário atualizar a rota da `PerfilScreen` na `MainActivity`, adicionando um novo parâmetro obrigatório: `/{idade}`.

```kotlin
composable(
    route = "perfil/{nome}/{idade}",
    arguments = listOf(
        navArgument("nome") { type = NavType.StringType },
        navArgument("idade") { type = NavType.IntType }
    )
) {
    val nome: String? = it.arguments?.getString("nome", "Usuário Genérico")
    val idade: Int? = it.arguments?.getInt("idade", 0)
    PerfilScreen(
        modifier = Modifier.padding(innerPadding),
        navController,
        nome!!,
        idade!!
    )
}
```

Com isso, a rota passa a exigir dois argumentos: `"nome"` e `"idade"`.

Foi definida uma lista de argumentos esperados utilizando `navArgument`, especificando o tipo de cada um:

- `"nome"` como `String`
- `"idade"` como `Int`

Diferente do caso da `PedidosScreen`, onde o tipo não foi explicitamente informado (pois o padrão é `String`), aqui foi necessário declarar o tipo de `"idade"` como inteiro.

Na sequência, foi criada uma outra variável para recuperar os valores passados na navegação:

```kotlin
val nome: String?
val idade: Int?
```

Ambas seguem a mesma lógica de segurança utilizando `it.arguments?`, evitando erros caso os argumentos não estejam disponíveis.

Além disso, foram definidos valores padrão:

- `"Usuário Genérico"` para `nome`
- `0` para `idade`

Esses valores funcionam como fallback para evitar falhas inesperadas.

Por fim, na chamada da `PerfilScreen`, os valores são forçados como não nulos utilizando `!!`:

```kotlin
nome!!
idade!!
```
Também foi necessário ajustar a navegação na `MenuScreen` para garantir que ambos os valores sejam informados.

```kotlin
onClick = { navController.navigate("perfil/Fulano de Tal/27") }
```

Nesse caso:
- `27` é o valor enviado para o parâmetro `"idade"`

Como a rota foi definida como `perfil/{nome}/{idade}`, é obrigatório passar os dois valores na navegação, respeitando a ordem em que foram declarados.

Dessa forma, ao clicar no botão, a `PerfilScreen` receberá corretamente ambos os parâmetros e poderá utilizá-los conforme necessário na interface.

Caso algum dos parâmetros não seja informado, a navegação poderá falhar ou gerar erros na aplicação.

Na `PerfilScreen`, foi adicionado um novo parâmetro `idade` do tipo `Int`, que será recebido a partir da navegação e utilizado na interface.

```kotlin
fun PerfilScreen(
    modifier: Modifier = Modifier,
    navController: NavController,
    nome: String,
    idade: Int
) {
    Box(
        modifier = modifier
            .fillMaxSize()
            .background(Color(0xFFFAF091))
            .padding(32.dp)
    ) {
        Text(
            text = "PERFIL - $nome tem $idade anos",
            fontSize = 24.sp,
            fontWeight = FontWeight.Bold,
            color = Color.White
        )
    }
}
```

Dentro do componente `Text`, foi realizada a interpolação de strings para exibir tanto o nome quanto a idade do usuário:

```kotlin
"PERFIL - $nome tem $idade anos"
```

Com isso, ao clicar no botão na `MenuScreen` que envia os parâmetros `"Fulano de Tal"` e `27`, o texto exibido na tela será:

```text
PERFIL - Fulano de Tal tem 27 anos
```

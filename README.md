# Trabalho Prático sobre Testes de Software

## JabRef

JabRef é um sistema multi-plataforma e open-soure para gerenciamento de referências e citações escrito na linguagem Java. Esse sistema auxilia os usuários a coletar e organizar referências bibliográficas e citações. Além disso, o sistema também auxilia na busca por um *paper* específico e na descoberta das pesquisas mais recentes de um tópico.

### Exemplo 1: OrdinalsToSuperscriptFormatter.format

Uma das funcionalidades do JabRef é a exportação de citações e referências no formato LaTeX/BibTex, para uso com essas tecnologias de criação de documentos. Para implementar parte dessa funcionalidade, o JabRef possui uma classe `OrdinalsToSuperscriptFormatter`, que contém um método `format`, que possui a seguinte assinatura:

```Java
public String format(String value)
```

Esse método recebe uma string contendo um número ordinal representado por um ou mais dígitos seguido de uma abreviação ordinal em inglês ("st", "nd", "rd", "th") e retorna uma string em formato LaTeX, na qual a abreviação ordinal aparece como um sobrescrito no número após a compilação do fonte LaTeX.

Um dos testes de unidade para esse método é o seguinte:

```Java
public void replacesSuperscript() {
    expectCorrect("1st", "1\\textsuperscript{st}");
    expectCorrect("2nd", "2\\textsuperscript{nd}");
    expectCorrect("3rd", "3\\textsuperscript{rd}");
    expectCorrect("4th", "4\\textsuperscript{th}");
    expectCorrect("21th", "21\\textsuperscript{th}");
}
```

Esse teste é bem simples e, para compreendê-lo, basta entender o papel do método `expectCorrect` chamado pelo método de teste anterior. A implementação desse método pode ser vista a seguir:

```Java
private void expectCorrect(String input, String expected) {
    assertEquals(expected, formatter.format(input));
}
```

Como pode se verificar no código anterior, o método `expectCorrect` recebe como entrada a string a ser convertida para o formato LaTeX e a string que é esperada após a conversão. O método sob teste (`format`) é, então aplicado sobre a string dada como entrada e o valor retornado é comparado com o esperado para verificar se as strings são iguais. Essa comparação é feita pelo método `assertEquals` do framework de teste JUnit. O método auxiliar `expectCorrect` é utilizado no teste anterior para evitar a repetição do código das chamadas do método `format` dentro das asserções do JUnit.

## Scrapy

Scrapy é um framework Python de alto nível para *web-scrapping*, isto é, extração automática de informação estruturada a partir de páginas web. O framework Scrapy permite navegar de forma automática por diversas páginas web (*crawling*) e extrair dados a partir do código HTML dessas páginas. Esse framework pode ser utilizado para aplicações que vão desde a mineração e monitoramento de dado a construção de testes de sistema para sistemas web.

### Exemplo 2: Headers.get e Headers.getlist

Uma parte fundamental do framework Scrapy é a criação de requisições HTTP/HTTPS para acessar os sites que serão processados e terão os dados extraídos pelo Scrapy. Parte do processo para criar uma requisição HTTP/HTTPS envolve criar um cabeçalho, que deve ser enviado justamente a um conteúdo opcional (corpo) em um documento que é enviado ao servidor do site utilizando algum protocolo de comunicação. A classe `Headers` representa um cabeçalho HTTP/HTTPS *case insensitive* criado com pares chave-valor (assim como os dicionários da linguagem Python). O método `get` permite recuperar o valor de um atributo do cabeçalho atual dado, como parâmetro, a chave associada. A assinatura desse método é a seguinte:

```Python
def get(self, key, def_val=None):
```

O método `get` também recebe um valor a ser retornado caso a chave não exista no cabeçalho. Esse método retorna o valor associado à chave dada como argumento, ou `None` se a chave dada não existir no cabeçalho. Um método alternativo `getlist` também é implementado e possui comportamento bastante similar ao `get`, com a diferença de retornar uma lista de valores associados a uma chave (uma lista vazia ou com um único valor especial indicado como parâmetro é retornada caso a chave não exista). Este último método é útil para chaves associadas a mais de um valor no cabeçalho, pois o método `get` retorna apenas o último valor associado a uma chave caso existam múltiplos valores. O método `getlist` possui o seguinte cabeçalho:

```Python
def getlist(self, key, def_val=None):
```

O seguinte teste de unidade testa o comportamento dos métodos `get` e `getlist` sobre um cabeçalho HTTP simples (criado com duas chaves associadas a valores únicos) quando se tenta acessar uma chave inexistente:

```Python
    def test_basics(self):
        h = Headers({'Content-Type': 'text/html', 'Content-Length': 1234})
        assert h['Content-Type']
        assert h['Content-Length']

        self.assertRaises(KeyError, h.__getitem__, 'Accept')
        self.assertEqual(h.get('Accept'), None)
        self.assertEqual(h.getlist('Accept'), [])

        self.assertEqual(h.get('Accept', '*/*'), b'*/*')
        self.assertEqual(h.getlist('Accept', '*/*'), [b'*/*'])
        self.assertEqual(h.getlist('Accept', ['text/html', 'images/jpeg']),
                         [b'text/html', b'images/jpeg'])
```

No teste de unidade anterior, é instanciado um objeto da classe `Headers` utilizando um dicionário da linguagem Python. O objeto instanciado possui duas chaves: `'Content-Type'` e `'Content-Length'`, associadas aos valores `'text/html'` e `1234`, respectivamente. O método de teste inicia verificando se ambas as chaves existem e não são associadas a valores nulos utilizando o operador de acesso a elementos (representado por um valor entre colchetes), que é sobrescrito na classe `Headers` e delega sua chamada para o método `get`, o qual está sob teste. A verificação anterior é feita com o comando `assert` que, na linguagem Python, dispara uma exceção caso o valor que o segue seja `False` ou equivalente (como é o caso de um valor nulo). Como uma exceção em um método de teste faz o teste de unidade falhar, o uso do assert nesse caso método é equivalente a utilizar um dos métodos de testes fornecidos pelo framework de teste.

Em seguida, o método de teste anterior testa se uma exceção do tipo `KeyError` é levantada ao tentar acessar uma chave inexistente com o operador de acesso a elementos (também acessado por uma chamada ao método mágico `__getitem__` em Python) e se uma lista vazia e o valor `None` são retornados pelos métodos `getlist`e `get`, respectivamente, ao tentar acessar uma chave também inexistente.

Por fim, o método de teste verifica se o valor especificado para chaves inexistentes pelo parâmetro adicional `def_val` é retornado ao passar uma chave inexiste como parâmetro para os métodos `get` e `getlist`.

### Exemplo 3: Headers (construtor), Headers.__setitem_, Haders.setdefault e Headers.setlist

A classe Headers, apresentada no exemplo anterior, também implementa métodos "set", que permitem associar valores às chaves de um cabeçalho HTTP/HTTPs após a criação de um objeto do tipo Headers. Também é implementado um "setter" da linguagem Python (acessado alternativamente por uma chamada ao método `__setitem__`) que deve ter o mesmo efeito de `setdefault`, com a diferença de permitir uma atribuição ao item como se fosse um atributo do objeto. A assinatura desses métodos é a seguinte:

```Python
def __setitem__(self, key, value):
def setdefault(self, key, def_val=None):
def setlist(self, key, list_):
```

O Scrapy implementa uma validação para a construção de cabeçalhos e atribuição de valores às suas chaves. Segundo a especificação do sistema, nenhum objeto Python pode ser atribuído como valor a uma chave de um cabeçalho HTTP/HTTPS representado pela classe `Headers`. O seguinte teste de unidade é utilizado para verificar o comportamento do construtor e dos métodos de atribuição de valores a chaves quando um objeto é atribuido:

```Python
def test_invalid_value(self):
    self.assertRaisesRegex(TypeError, 'Unsupported value type',
                            Headers, {'foo': object()})
    self.assertRaisesRegex(TypeError, 'Unsupported value type',
                            Headers().__setitem__, 'foo', object())
    self.assertRaisesRegex(TypeError, 'Unsupported value type',
                            Headers().setdefault, 'foo', object())
    self.assertRaisesRegex(TypeError, 'Unsupported value type',
                            Headers().setlist, 'foo', [object()])
```

O método de teste anterior atribui um objeto a uma chave de quatro maneiras distintas: utilizando o construtor da classe `Headers`; utilizando o "setter" da linguagem Python (por meio de uma chamada ao método mágico `__setitem__`); utilizando o método `setdefault` e, por fim, utilizando o método `setlist`. Em todos os quatro casos é esperado que uma exceção do tipo `TypeError` seja levantada e, também, que essa exceção contenha a mensagem `'Unsupported value type'`. A verificação anterior é feita utilizando o método `assertRaisesRegex`, que é fornecido pelo framework de testes unittest, padrão na linguagem Python e utilizado na implementação dos testes do Scrapy.

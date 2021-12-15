# pds-softwareTests
Repositório feito para realizar o trabalho prático de testes de softwares feitos na disciplina de Prática de Desenvolvimento de Software 2021/2


Nesse trabalho, iremos documentar alguns testes feito em um SDK para comunicação de API sobre o jogo Magic, the Gathering https://github.com/MagicTheGathering/mtg-sdk-python, facilitando toda criação dos objetos em código e uso dos dados sobre as cartas do jogo. Iremos abordar 3 testes, sendo eles:

1. test_find_returns_card(self) (https://github.com/MagicTheGathering/mtg-sdk-python/blob/master/tests/test_card.py): Esse teste se refere à validar uma busca por uma carta específica, verificando todas as propriedades do objeto retornado se está de acordo com o esperado. 

```python
class TestCard(unittest.TestCase):
    def test_find_returns_card(self):
        with vcr.use_cassette('fixtures/choice_of_damnations.yaml'):
            card = Card.find(88803)
            self.assertEqual('Choice of Damnations', card.name)
            self.assertEqual('{5}{B}', card.mana_cost)
            self.assertEqual(6, card.cmc)
            self.assertEqual('Sorcery — Arcane', card.type)
            self.assertTrue('Black' in card.colors)
            self.assertEqual(['B'], card.color_identity)
            self.assertTrue('Sorcery' in card.types)
            self.assertTrue('Arcane' in card.subtypes)
            self.assertEqual('Rare', card.rarity)
            self.assertEqual('SOK', card.set)
            self.assertEqual('Saviors of Kamigawa', card.set_name)
            self.assertEqual("Target opponent chooses a number. You may have that player lose that much life. If you don't, that player sacrifices all but that many permanents.", card.text)
            self.assertEqual("\"Life is a series of choices between bad and worse.\" —Toshiro Umezawa", card.flavor)
            self.assertEqual('Tim Hildebrandt', card.artist)
            self.assertEqual('62', card.number)
            self.assertEqual(88803, card.multiverse_id)
            self.assertEqual('http://gatherer.wizards.com/Handlers/Image.ashx?multiverseid=88803&type=card', card.image_url)
            self.assertTrue(len(card.rulings) > 0)
            self.assertTrue({"name":"Scelta della Dannazione","text" : "L'avversario bersaglio sceglie un numero. Puoi far perdere a quel giocatore un ammontare di punti vita pari a quel numero. Se non lo fai, quel giocatore sacrifica tutti i permanenti tranne un numero di permanenti pari al numero scelto.","flavor" : "\"La vita è una sequela di scelte tra male e peggio.\"\n—Toshiro Umezawa","imageUrl":"http://gatherer.wizards.com/Handlers/Image.ashx?multiverseid=105393&type=card","language":"Italian","multiverseid":105393} in card.foreign_names)
            self.assertTrue('SOK' in card.printings)
            self.assertEqual("Target opponent chooses a number. You may have that player lose that much life. If you don't, that player sacrifices all but that many permanents.", card.original_text)
            self.assertEqual('Sorcery - Arcane', card.original_type)
            self.assertTrue({"format":"Commander","legality":"Legal"} in card.legalities)
            self.assertEqual('224a2a63-7be6-5e06-bf6b-e667727bf80b', card.id)**
```
Um teste bem simples de validação de dados específicos, uma vez que já se tem todos os dados do objeto esperado.

2. test_all_with_params_return_cards(self) (https://github.com/MagicTheGathering/mtg-sdk-python/blob/master/tests/test_card.py): Ainda no mesmo arquivo, temos um teste um pouco diferente, ao invés de verificar os dados de cada objeto retornado, verificamos se ele está retornando uma lista de objetos dada uma nova busca.

```python
def test_all_with_params_return_cards(self):
    with vcr.use_cassette('fixtures/legendary_elf_warriors.yaml'):
        cards = Card.where(supertypes='legendary') \
                    .where(subtypes='elf,warrior') \
                    .all()

        self.assertTrue(len(cards) >= 13)
```
Podemos observar que é retornado uma lista quando os valores não são específicos o suficiente para diferenciar os objetos, o que valida o funcionamento da busca por lista e do comportamento da classe 'Card'.

3.  test_where_filters_on_name(self) (https://github.com/MagicTheGathering/mtg-sdk-python/blob/master/tests/test_set.py): Para finalizar, iremos testar não as cartas dessa vez, mas sim a coleção na qual elas saem, uma vez que novas cartas são lançadas no jogo através de coleções, podemos agrupar e categorizar, tanto as cartas, quanto essas coleções.

```python
def test_where_filters_on_name(self):
    with vcr.use_cassette('fixtures/filtered_sets.yaml'):
        sets = Set.where(name='khans of tarkir promos').all()
            
        self.assertEqual(1, len(sets))
        self.assertEqual('PKTK', sets[0].code)
```
Podemos ver, nesse trecho de código, que a classe Set é testada e que funciona de maneira similar à classe Card testada anteriormente, uma vez que pode retornar um ou vários objetos de uma vez em lista, e que, também, possui parâmetros de buscas de maneira simila. No mais, garantimos que a informação está correta nos campos solicitados e que os campos de busca funcionam como deveriam funcionar, adicionando uma segurança a mais no caso de alteração do funcionamento da classe 'Set'.

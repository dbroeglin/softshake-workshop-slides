!SLIDE subsection
# Introduction à Ruby

«Ruby is designed to make programmers happy» Yukihiro Matsumoto

!SLIDE bullets incremental small
## Particularités du langage

- Perl + Lisp + SmallTalk
- Tout est objet
- Tout est expression
- Interprété et *fortement* dynamiquement typé
- Réflexif
- Orienté DSL (meta-programmation)


!SLIDE bullets small
## Interpréteur de commandes (Read Eval Print Loop)

    @@@ sh
    irb
    irb(main):001:0> 1 + 2
    => 3

!SLIDE bullets small
## Tout est ...

## ... objet

    @@@ Ruby
    10.abs
    true.class

## ... expression

    @@@ Ruby
    unhealthy_food = if true
      "chunky bacon"
    else
      "potatoes"
    end


!SLIDE bullets small
## Dynamiquement et fortement typé

    @@@ Ruby
    a = "potatoes"
    puts a.class

    a = 1 + 5
    puts a.class

    true.class

    String.class

!SLIDE bullets small
## "Duck typing" (1/2)

Si ça ressemble à un canard, si ça nage comme un canard et si ça 
cancane comme un canard, c'est qu'il s'agit sans doute d'un canard.

![quack](duck_typing.jpg)

!SLIDE bullets small
## "Duck typing" (2/2)

    @@@Ruby
    def compute(a, b, c)
      (a + b) * c
    end
    
    compute(1, 2, 3)
    compute("Hello ", "world! ", 3)

!SLIDE bullets small
.notes todo: keep? 
## Réflexif

    @@@ Ruby
    [].respond_to?(:each)

!SLIDE bullets small
## Les Blocs

Sorte de fonction anonyme.
Deux formes syntaxiques: 

    @@@ Ruby
    10.times { puts "youpi" }

    10.times do 
      puts "youpi"
    end

Un exemple pour les amateurs de Javascript:

    @@@ Ruby
    ["foo", "bar"].each { |s| puts s }

    # version javascript:
    ["foo", "bar"].forEach(function(s) { console.log (s); });

Une utilisation idiomatique:

    @@@ Ruby
    ['dbroeglin', 'david_hodgetts'].map { |u| "@#{u}"}.join(", ")

!SLIDE bullets small
## Parenthèses optionnelles

Exemple tiré de rspec (framework de test)

    @@@ Ruby
    it "should work" do
      my_obj.my_method.should be_valid
    end

Avec les parenthèses:

    @@@ Ruby
    it("should work") do
      my_obj.my_method().should(be_valid())
    end

!SLIDE bullets small
## Symboles

Sorte de "string" léger et immutable  

    @@@ Ruby
    :first_name.class

    "first_name".to_sym
    :first_name.to_s

    User.find_by(:first_name => "Dan")

Pour les pratiquants c# et Java:

    @@@ Java
    // c#
    public const string FIRST_NAME_COL = "first_name";

    // Java
    public static final String FIRST_NAME_COL = "first_name"

!SLIDE bullets small
## Définitions litérales pour les tableaux et les tableaux associatifs

    @@@ Ruby
    my_array = [0, 1, 2]
    my_array[0]

    hash_old_school = {:a_key => 10, :another_key => "another value"}
    hash_new_school = {a_key: 10, another_key: "another value"}
    my_hash[:a_key]



!SLIDE bullets small
## Objet

    @@@ Ruby
    class Person
      def greet
        puts "Hello!"
      end
    end

    p = Person.new
    p.greet



!SLIDE bullets small
## Encapsulé, pas d'accès direct à l'état interne
L'esprit 'smalltalk'. On envoit un message à un objet.

    @@@ Ruby
    class Person
      attr_accessor :name
    end

    p = Person.new
    p.name = "pierre"
    puts p.name

L'accés à l'attribut "name" se fait par l'intermédiaire d'une paire de "getter/setter"

!SLIDE bullets small
## Héritage simple

    @@@ Ruby
    class Person 
      def greet 
        puts "Hello!"
      end
    end

    class AustralianPerson < Person 
      def greet 
        puts "Hello, mate!"
      end
    end

    Person.new.greet
    AustralianPerson.new.greet


!SLIDE bullets small
## Monkey patching

    @@@ Ruby
    class String
      def palindrome?
        self == self.reverse
      end
    end

    'deified'.palindrome?

Rails rajoute pas mal de fonctionalité au types standard.  
On pourrait dire que Rails spécialise le langage pour son domaine d'intérêt.
    
    @@@ Ruby
    due_date = Date.today + 1.week


!SLIDE bullets small 
## En conclusion:

- Ruby est flexible et dynamique.
- Il se prête à l'écriture de DSL (langage dédié).
- Peut-on dire alors que Rails est un DSL pour le web?

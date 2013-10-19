!SLIDE subsection
# Introduction à Ruby

«Ruby is designed to make programmers happy» Yukihiro Matsumoto

!SLIDE bullets incremental small
# Particularités du langage

- Perl + Lisp + SmallTalk
- Tout est objet
- Tout est expression
- Interprété et *fortement* dynamiquement typé
- Réflexif
- Orienté DSL (meta-programmation)


!SLIDE bullets small
# Interpréteur de commandes (REPL)

    @@@ sh
    irb
    irb(main):001:0> 1 + 2
    => 3

!SLIDE bullets small
# Tout est ...

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
# Dynamiquement et fortement typé

    @@@ Ruby
    a = "potatoes"
    puts a.class

    a = 1 + 5
    puts a.class

    true.class

    String.class

!SLIDE bullets small
# "Duck typing" (1/2)

Si ça ressemble à un canard, si ça nage comme un canard et si ça 
cancane comme un canard, c'est qu'il s'agit sans doute d'un canard.

![quack](duck_typing.jpg)

!SLIDE bullets small
# "Duck typing" (2/2)

    @@@Ruby
    def compute(a, b, c)
      (a + b) * c
    end
    
    compute(1, 2, 3)
    compute("Hello ", "world! ", 3)

!SLIDE bullets small
.notes todo: keep? 
# Réflexif

    @@@ Ruby
    [].respond_to?(:each)

!SLIDE bullets small
# Bloc party

    @@@ Ruby
    10.times { puts "youpi" }

    10.times do 
      puts "youpi"
    end

    ["foo", "bar"].each { |s| puts s }

    # in js speak
    ["foo", "bar"].forEach(function(s) { 
      console.log (s); });

    [1, 10, 25].map { |d| "#{d} km"}.join(", ")

!SLIDE bullets small
# Parenthèses optionnelles

    @@@ Ruby
    # exemple rspec (framework de test)
    it "should work" do
      my_obj.my_method.should be_valid
    end

    # au lieu de :
    it("should work") do
      my_obj.my_method().should(be_valid())
    end

!SLIDE bullets small
# Symboles 

Sorte de "string" léger et immutable  

    @@@ Ruby
    :first_name.class
    
    "first_name".to_sym
    :first_name.to_s

    User.find_by(:first_name => "Dan")

    # en C#:
    public const string FIRST_NAME_COL = "first_name";

    # en Java:
    public static final String FIRST_NAME_COL = "first_name"

!SLIDE bullets small
# Structures de données avec définitions litérales

    @@@ Ruby
    my_array = [0, 1, 2]
    my_array[0]

    hash_old_school = {:a_key => 10, :another_key => "another value"}
    hash_new_school = {a_key: 10, another_key: "another value"}
    my_hash[:a_key]   



!SLIDE bullets small
# Objet

    @@@ Ruby
    class Person
      def greet
        puts "Hello!"
      end
    end

    p = Person.new
    p.greet

!SLIDE bullets small
.notes TODO: keep ?
# Encapsulé, pas d'accès direct à l'état interne

    @@@ Ruby
    class Person

      def name
        @name
      end

      def name=(value)
        @name = value
      end

    end

    p = Person.new
    p.name = "pierre"
    puts p.name


!SLIDE bullets small
.notes TODO: keep ?
# la même chose 

    @@@ Ruby
    class Person
      attr_accessor :name
    end

    p = Person.new
    p.name = "pierre"
    puts p.name

!SLIDE bullets small
# Héritage simple

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
# Monkey patching
![monkey patch](monkey_patch.jpg)


    @@@ Ruby
    class String
      def palindrome?
        self == self.reverse
      end
    end

    'deified'.palindrome?


!SLIDE bullets small incremental
# Ruby : flexible et dynamique

- DSL (langage dédié) ready
- Rails un DSL pour le web ?

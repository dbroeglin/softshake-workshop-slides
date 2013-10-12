!SLIDE subsection
# Introduction à Ruby

"ruby is designed to make programmers happy" (Yukihiro Matsumoto)



!SLIDE bullets incremental small
# Particularités du langage

- Perl, Lisp, SmallTalk
- tout est objet
- tout est expression
- interprété et dynamiquement typé
- narcissique (oups on voulait dire -> réflexif)
- dsl orienté

!SLIDE bullets small
# tout est objet

		@@@ Ruby
		10.abs
		true.class

!SLIDE bullets small
# tout est expression
	
		@@@ Ruby
 		unhealthy_food = if true then 
 			"chunky bacon" 
 		else "potatoes" 
 		end

!SLIDE bullets small
# dynamiquement typé

![quack](duck_typing.jpg)

!SLIDE bullets small
# réflexif

		@@@ Ruby
 		[].respond_to?(:each)

!SLIDE bullets small
# parens optionnel

	@@@ Ruby
	# exemple rspec (framework test)
	it "should work" do
		my_obj.my_method.should be_valid
	end

!SLIDE bullets small
# bloc party

	@@@ Ruby
	10.times { puts "youpi" }

	10.times do 
		puts "youpi"
	end

	["foo", "bar"].each { |s| puts s }

	# in js speak
	["foo", "bar"].forEach(function(s) { 
		console.log (s); });

!SLIDE bullets small
# objet

	@@@ Ruby
	class Person
	end

	p = Person.new


!SLIDE bullets small
# encapsulé, pas d'accés direct à l'état interne

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
# la même chose 

	@@@ Ruby
	class Person
		attr_accessor :name
	end

	p = Person.new
	p.name = "pierre"
	p.name => "pierre"




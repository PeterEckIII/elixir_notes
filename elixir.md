# Elixir
### _General Info_:
**Arity & Function Notation**
* Elixir and, by extension, Erlang use a functional notation syntax to communicate information about the function itself.
* Arity is a term that represents the number of arguments a function takes
* Example: 
    ```
        def greet(name:name) do
    	   IO.puts name
        end
    ```
For this example, the `greet` function’s notation would be: `greet/1` which indicates the greet function takes one argument. 

### **Collections**:
##### _Lists_:
* Prepending vs. Appending a list:
	* Elixir processes prepending quicker than appending. 
	* An example of prepending:
		```
		list = [3.14, :pie, "Apple"]
		["Honeycrisp" | list]
		// ["Honeycrisp", 3.14, :pie, "Apple"]
		```
	 * An Example of appending:
    	```
    		list ++ ["Gala"]
    		// [3.14, :pie, "Apple", "Gala"]
    	```
* List concatenation uses the `++/2` function to add two lists together:
	```
	    [1,2] ++ [3,4]
	    // [1,2,3,4]
	```
	
* List subtraction uses the `--/2` to subtract elements from a list:
    ```
        ["foo", :bar, 42] -- [42]
		// ["foo", :bar"]
		
		["foo", :bar, 42] -- [42]
		// ["foo", :bar"]
    ```
* Duplicate Values in List Subtraction:
    * For every element on the right argument, the first occurrence of it gets removed from the left:
	```
		[1,2,2,3,2,3] -- [1,2,3,2]
		// [2,3]
	```
    * List Subtraction uses strict comparison to match the values:
    ```
			[2] --- [2.0]
			// [2]
			vs.
			[2.0] -- [2.0]
			// []
	```
* Head / Tail:
	* The `Head` is the list’s first element:
	    ```
	        hd [3.14, :pie, “Apple”]
		    // 3.14
	    ```
	* The `Tail` is a list of the remaining elements
	    ```
	        tl [3.14, :pie, "Apple"]
	    	// [:pie, "Apple"]
	    ```
	* You can pattern match with lists as well:
	    ```
	        [head | tail] = [3.14, :pie, "Apple"]
		    head // 3.14
		    tail // [:pie, "Apple"]
	    ```
##### _Tuples_:
* Similar to lists, but stored contiguously in memory
* This means accessing their length is fast, but modification is expensive
* Tuples are used to return additional information from functions (esp in Pattern Matching!)
    ```
        File.read("path_to_existing_file.csv")
        // {:ok, "contents"}
        File.read("path_to_nonexistant_file.csv")
        // {:error, :enoent}
    ```
##### _Keyword Lists_:
* Keyword Lists (and Maps) are associative collections in Elixir (think Object Literals in JavaScript), however they have some restrictions:
	* The KW list must be made of two-element tuples whose first element (key) is an atom
	`[ { :foo, "bar" }, {:hello, :world"} ]`
	* Keys are ordered and do not have to be unique
* For the reasons stated above, KW Lists are commonly used to pass options to functions

##### _Maps_:
* In Elixir, Maps are the “go-to” key-value store. 
* Unlike KW Lists they allow keys of any type and are unordered
    ```
        myMap = %{:foo => "Bar", "hello" => :world}
        myMap[:foo] // "bar"
        myMap["hello"] // :world
    ```
* Variables are allowed as map keys as well:
    ```
        myVar = "Hello"
        %{key => "world"}
        // %{"hello" => "world"}
    ```
* If a duplicate key is added to the Map it will replace the former value:
    ```
        %{:foo => "bar", :foo => "Hello World"}
        // %{:foo => "bar", :foo => "Hellow World"}
        %{:foo => "bar", :foo => "Hello World"}
        // %{:foo => "Hello World"}
    ```
* There is a special syntax when using only atoms as keys:
    ```
        myMap = %{foo: "bar", hello: "world"}
        mySpecialMap = %{:foo => "bar", :hello => "world"}
        myMap == mySpecialMap
        // true
    ```
* There is also a special syntax for accessing atom keys:
    ```
        map = myMap = %{foo: "bar", hello: "world"}
        map.hello
        // "world"
    ```
* Maps also have their own syntax for updating values (immutably! This creates a new Map):
    ```
        myMap = %{foo: "bar", hello: "world"}
        %{map | foo: "baz"}
        // %{foo: "baz", hello: "world"}
    ```
	* NOTE: this only works for updating existing keys. If you need to add a key, try the `Map.put/3` function:
	    ```
	        myMap = %{foo: "bar", hello: "world"}
	        Map.put(map, :baz, "bur")
	        %{foo: "baz", hello: "world, :baz: "bur"}
	    ```
##### _Enums_:
* All Collections are enumerable **except** Tuples
* There are over 70 Enum functions available natively in Elixir. Here are some examples:
    * **all?/2** — checks a condition for all elements in the list
        ```
            Enum.all?(["foo", "bar", "baz"], fn(s) -> String.length(s) == 3 end)
            // true
        ```
    * **any?/2**— checks a condition for any elements in the list
        ```
            Enum.any?(["foo", "barb", "bazar"], fn(s) -> String.length(s) == 3 end)
            // true
        ```
    * **chunk_every/2** — breaks the collection into smaller groups based on size
        ```
            Enum.chunk_every([1,2,3,4,5,6], 2)
            // [[1,2], [3,4], [5,6]]
        ```

    * **chunk_by/2** — breaks the collection based on something other than size
        ```
            `Enum.chunk_by(["one", "two", "three", "four", "five", "six"], fn(x) -> String.length(x) end)`
            `// [["one", "two"], ["three"], ["four", "five"]]`
        ```
    * **map_every/3** — applies a function to every nth item
        ```
            Enum.map_every([1,2,3,4,5,6,7,8], 3, fn x -> x + 1000 end)
            // [1001, 2, 3, 1004, 5, 6, 1007, 8]
        ```
    * **each/2** — iterate over a collection without producing a new value:
        ```
            Enum.each(["one", "two", "three"], fn(s) -> IO.puts(s) end
            // one
            // two
            // three
            // :ok
        ```
    * **min/1 AND max/1** — finds the minimum or maximum value of a collection:
        ```
            Enum.min([5,3,0,-1])
            // -1
            Enum.max(1,2,5,10)
            // 10
        ```
    * **filter/2** — filters a collection based on a given function
        ```
            Enum.filter([1,2,3,4], fn(x) -> rem(x, 2) == 0 end)
            // [2,4]
        ```
    * **reduce/3** — reduces a collection into one value
        ```
            Enum.reduce(1,2,3,4], 10, fn(x, acc} -> x + acc end)
            // 16
        ```
    * **sort/1** — sorts the list in term ordering (per Erlang)
        ```
            Enum.sort([5, 6, 1, 3, -1, 4])
            // -1, 1, 3, 4, 5, 6
        ```
    * **sort/2** — allows us to provide sort function of our own
        ```
            Enum.sort([%{:val => 4}, %{:val => 1}], fn(x,y) -> x[:val] > y:[val] end)
            // [%{ count: 1 }, %{ count: 4 }]
        ```
    * **uniq/1** — remove duplicates
        ```
            Enum.uniq([1,2,3,1,1,5,6,7,3,4,6,5,4,3])
            // [1,2,3,4,5,6,7]
        ```
    * **uniq_by/2** — removes duplicates with a function
        ```
            Enum.uniq_by([%{x: 1, y: 1}, %{x: 2, y:1}, %{x:3, y:3}], fn coord -> coord.y end)
            // [%{x:1, y:1}, %{x:3, y:3}]
        ```
### _Pattern Matching_:
* The `=` sign isn't just for assigning values in Elixir, it's also used for Pattern Matching
* **The Elixir `=` operator works similarly to the equals sign in mathematics -- the left-hand-side must equal (or "match") the right-hand0sde for the equation to be valid**
* An example:
    ```
        2 + 2 = 5
        // (MatchError) no match of right hand side value: 4
    ```
* Here, Elixir is telling us that `2+2 = 5` is not valid. The left side has to evaluate to the same as the right side.
* The same idea applies to Strings in Elixir:
    ```
        "dog" = "dog"
        // "dog"
        "dog" = "cat"
        // (MatchError) no match of right hand side value: "cat"
    ```
* **Pattern Matching with Lists**
    ```
        list = [
            %{name: "Peter", age: 28},
            %{name: "Katie", age: 28},
            %{name: "Ben", age: 24},
            %{name: "Mom", age: 52}
        ]
    ```
    * With this list we can destructure the variables we want
        ```
            [first, second | rest] = list
            first
            // %{name: "Peter", age: 28}
            second
            // %{name: "Katie", age: 28}
            rest
            // [%{name: "Ben", age: 24}, %{name: "Mom", age: 52}]
        ```
    * From here you can further destructure the `rest` list
        ```
            [fourth | remainder] = rest
            fourth
            // %{name: "Ben", age: 24}
            remainder
            // %{name: "Mom", age: 52}
        ```
    * Due to the way Elixir handles pattern matching we should always be careful to provide the correct number of arguments to the match. For this reason it is best to destructure one variable at a time
        ```
            // Ok :/
            [first, second, third | rest] = list
            
            // Best! :)
            [first | rest] = list
            [second | remaining] = rest
            [third | extra] = remaining
        ```
* **Pattern Matching with Maps**:
    *  Elixir makes it easy to extract values from a Map:
        ```
            person = %{name: "Peter", age: 28}
            person.name
            // "Peter"
            person.age
            // "Peter"
        ```
    * But what if you want to pull both values out at once
        ```
            %{name: name, age: age} = person
        ```
    * Wait...the left is clearly not the same as the right here. How can this be valid?
    * This is a pattern matching trick that allows you to use the left-hand-side to assigned multiple variables to a Map -- it doesn't have to match the right-side exactly
        ```
            name
            // "Peter"
            age
            // 28
        ```
    * We can also extract variables nested within a List of Maps
        ```
            list = [
                %{name: "Peter", age: 28},
                %{name: "Katie", age: 28},
                %{name: "Ben", age: 24},
                %{name: "Mom", age: 52}
            ]
            [first_person = %{name: first_name} | others] = list
            first_person
            // %{name: "Peter", age: 28}
            first_name
            // "Peter"
        ```
    * Here we're pulling out three specific values: `first_person`, `first_name`, and `others`
* **Pattern Matching Inside Functions**
    *  Pattern Matching can also be used as a shorthand return for a function:
        ```
            road = fn
                "high" -> "The noble route"
                "low" -> "Scum..."
            end
            road.("high")
            // "The noble route"
            road.("low")
            // "Scum..."
        ```
    * This syntax uses _function clauses_ to parse the logic and return
    * **Matching Maps Inside Functions**:
        * You can also match on Map keys and respond with different code based on the case
            ```
                greeting = fn
                    %{name: name} -> "Hello #{name}"
                    %{} -> "Hello, what's your name?"
                end
                greeting.(%{name: "Peter"})
                // "Hello Peter"
            ```
        * Here the function clause `%{name: name}` matches because the map we supply to the function (`%{name: "Peter"}`) contains a key called `"name"`
        * When we call the function with a map with a `name` key it will match to the first clause -- even if you pass in more keys in the map
            ```
                greeting.(%{name: "Peter", age: 28"})
                // "Hello Peter"
            ```
        * Now if we call the function with an empty map (or even a map with no `name` key) the second function clause will be met
            ```
                greeting.(%{})
                // "Hello, what's your name"
                greeting.(%{age: 28})
                // "Hello, what's your name?"
            ```
        * This is because Elixir will always match on the subset of the map -- for example:
            ```
                %{} = %{name: "Peter"}
                // %{"name": "Peter"}
            ```
        * Similar to the second clause in the `greeting` function, we're comparing an empty map on the left side to a map from the right side. 
        * When pattern matching like this, it's helpful to think of **the left side showing the keys that are absolutely required for the match to work, while the right side _must_ contain the same keys as the left side**, but it can contain more keys than that too
        * The above match succeeds because there are no keys _required_ by the left side
            ```
                greeting = fn
                    %{name: name} -> "Hello #{name}"
                    %{} -> "What's your name?"
                end
            ```
        * The first function clause in this `greeting` function dictates that the Map coming into the function **must** have a `name` key. If it does, regardless of what else is in there, it'll match
    * **Match anything**:
        * In order to provide some guards for your function you can provide a final argument that will catch all remaining cases not handled specifically:
            ```
                road = fn
                    "high" -> "A noble route",
                    "low" -> "Scum..."
                    _ -> "Which road will it be?"
                end
                road.("middle")
                // "Which road will it be?"
            ```
        * This helps ensure unanticipated behavior does not break your app
* Exercises:
    * Make a function that takes either a map containing a "name" and "age", or just a map containing "name". Change the output depending on if "age" is present. What happens if you switch the order of the function clauses? What can you learn from this?
    ```
        greeting = fn
            %{name: name, age: age} -> "Hello, #{name} (age: #{age})"
            %{name: name} -> "#{name}, what's your age again?"
            _ -> "What's your name and age?"
        end
        greeting.(%{name: "Peter", age: 28)
        // "Hello, Peter (age: 28)"
        greeting.(%{name: "Peter")
        // "Peter, what's your age again?"
        greeting.(%{pet: "Dog"})
        // "What your name and age"
    ```
        
### _Functions_
* Single argument functions
    ```
        greeting = fn (person) -> "#Hello #{person}" end
        greeting.("Peter")
        // "Hello Peter"
    ```

    ```
        c_to_f = fn (c) -> c * 1.8 + 32 end
        c_to_f.(0)
        // 32
        c_to_f(100)
        // 212
    ```

* Multiple-argument functions
    ```
        greeting = fn(name, gender, age) ->
            "Hello, #{name}! I see you are a #{gender} and you're #{age} years old"
        end
        greeting.("Peter", "male", 28)
        // "Hello, Peter! I see you are a male and you're 28 years old"
    ```
    * Elixir functions must take the exact number of arguments on invocation
        ```
            greeting.("Peter")
            // (BadArityError) <Function> with Arity 3 called with 1 argument ("Peter")
        ```
* Capture Operator
    * Elixir provides a shorthand for defining functions that can be used in lieue of the `fn` keyword
        ```
            capture_greeting - &("Hello #{&1}!")
            capture_greeting.("Peter")
            // "Hello Peter"
        ```
    * The `&` operator is used to wrap the code, as well as to represent the nth argument
    * From there you use the `&<number>` syntax to extract the value of the passed argument (in order!)
    * There is no behavior differences between the `fn` and the `&` keywords. The only difference is the syntax and the fact that you refer to arguments by their position, rather than by name
* Exercises:
    * Make a function which turns fahrenheit temperatures into celsius:
        ```
            f_to_c = fn(f) -> (f-32) * (5/9)
            f_to_c(212)
            // 100
            f_to_c(32)
            // 0 
        ```
    * Make a function which returns the number of seconds in the specified amount of days. For example, seconds.(2) should tell us how many seconds there are in 2 days:
        ```
            seconds_in_day = fn(days) -> days * 86400 end
            seconds_in_day(2)
            // 172800
        ```
    * Make a function which takes two maps with "age" keys in them and returns the average age:
        ```
            average_age = fn(%{age: age1}, ${age: age2}) ->
                {age1, age2} = {age1, age2}
                total = age1 + age2
                average = total / 2
                IO.puts "Average age: #{average}"
            end
            average_age.(%{age: 10}, %{age: 20})
            // "Average age: 15.0"
        ```

### Working with Strings, Input, and Output
* Some of the built-in functions that Elixir offers for strings are built into `modules` of code that can expose several functions.
* All modules begin with uppercase letters, which is their signature giveaway
* With functions on modules you can omit the `.` between the function name and the arguments when you invoke it:
    ```
        Bad :(
        String.reverse.("Peter")
        Good :)
        String.reverse("Peter")
    ```
* The functions below come from the `String` module, as you'll see they all begin with the same module name
    * **Reverse a string** -- `String.reverse/1`:
        *  arguments: string
        ```
            String.reverse("Peter")
            // "reteP"
        ```
    * **Splitting a string** -- `String.split/1`:
        * arguments: string
        ```
            String.split("Split this please it'll make me happy")
            // ["split", "this", "please", "it'll", "make", "me", "happy"]
        ```
    * **Replacing parts of a string** -- `String.replace/3`:
        * arguments: string, to change, change to
        ```
            String.replace("moo", "o", "e")
            // "mee"
            String.replace("the cow jumped over the moon", "o", "ee")
            // "the cee jumped eever the meeeen"
        ```
    * **Uppercase** -- `String.upcase/1`
        * arguments: string
        ```
            String.upcase("i'm not yelling")
            // "I'M NOT YELLING"
        ```
    * **Lowercase** -- `String.downcase/1`
        * arguments: string
        ```
            String.downcase("I'M WHISPERING")
            // "i'm whispering"
        ```
    * **Trim** -- `String.trim/1`
        * arguments: string
        ```
            name = "Peter\n   \n  "
            String.trim(name)
            // "Peter"
        ```
* **Input & Output**
    * _Making Prompts_
        *  Prompting users for information is simple via the `IO` module
            ```
                name = IO.gets("What is your name?")
                // What is your name?
                // User input: Peter
                name
                // "Peter\n"
            ```
        * We can also perform operations on this string, for example, to get rid of the `\n`
            ```
                String.trim(name)
                // "Peter"
            ```
        * Taking input and making it output
            ```
                name = IO.gets "What is your name? "
                age = IO.gets "How old are you?"
                IO.puts "Hello, #{String.trim(name)} ! You're #{String.trim(age)} years old!"
                // Whats is your name? 'Peter'
                // How old are you? 28
                // Hello Peter ! You're 28 years old!
            ```
    * **Immuatability**:
        * Immutability is at the heart of functional programming and refers to the discouragement of directly change variables
        * Immutability ensures safety in things like application state and encourages creating new variables as opposed to mutating existing variables
        * When you pass an argument to a function like `String.upcase` it does not mutate or change the variable, but instead creates a new reference point, mutates the original value, and saves the new value to the new reference point. An example:
            ```
                string = "I won't change"
                new_string = String.upcase(string)
                string
                // "I won't change"
                new_string
                // "I WON'T CHANGE"
            ```
        * The call to `String.upcase` does not change the original value `sentence` is set to. Regardless of any functions being run on `sentence`, it will always stay the same.
        * The only way you can change a variable is by re-assigning it's value:
            ```
                sentence = "I'm a sentence"
                sentence
                // "I'm a sentence"
                sentence = "I'm a new sentence"
                sentence
                // "I'm a new sentence"
            ```
        * Remember, we are not "re-assigning" explicitly here, but rather _matching_ the variable to a new value (via Pattern Matching!)
* Exercises:
    * Make a program that generates a very short story. Get it to take some input of a person, a place, and an object -- using `IO.gets/1` to combine all three into a sentence and output it with `IO.puts/1`
        ```
            short_story = fn -> 
                name = IO.gets("What is your name?)"
                place = IO.gets("Where do you wish to go?")
                possession = IO.gets("What is your most beloved possession")
                IO.puts("Hello brave Sir #{name}, I see you have set your sights on #{place}. Did you bring your #{possession}?")
            end
            short_story.()
            "What is your name?" Peter
            "Where do you wish to go?" Harrenhal
            "What is your most beloved possession?" Comb
            // "Hello brave Sir Peter, I see you have set your sights on Harrenhal. Did you bring your comb?"
        ```
* **Working with Lists**:
    * 
    

# DSL:
- A specialized language—either technical or human-readable—tailored to a 
specific business problem domain
- A Domain Specific Language is a programming language with a higher level of
abstraction optimized for a specific class of problems. A DSL uses the 
concepts and rules from the field or domain.
- In many cases, DSLs are intended to be used not by software people, but 
instead by non-programmers who are fluent in the domain the DSL addresses.

## Key Aspects of DSLs in DDD:
- Purpose: To bridge the gap between domain modeling (what the business wants) 
and implementation (code), reducing "incidental complexity".
- Ubiquitous Language Integration: The DSL uses the same vocabulary agreed 
upon by developers and domain experts, as defined in DDD's ubiquitous language.

## Types:
- Internal DSLs: 
  - Written within a host language (e.g., Fluent Interfaces in Java or Ruby) 
    to look like specialized commands.
  - Uses host syntax but mimics domain concepts

- External DSLs: 
  - Custom-built languages such as SQL or custom business rules engines.
  - Requires their own syntax parser

## Common Examples:
- SQL: For database queries.
- Cucumber/Gherkin: For behavior-driven testing.
- Custom Rules Engine: A specialized syntax for processing insurance claims or 
flight bookings.

## How Domains Evolve? & When you know you are there?:
- Glossary - useful terms
- Structured Glossary ~ "Ubiquitous Language" / Ontology
- Domain models: Glossary is represented as code
- Metamodels: 
  - metamodel defines the abstract syntax of a language
  - important ingredient of DSL
  - characterizes the model's relationship 
```code 
  - Abstract Syntax
    - Add(left, right), Multiply(left, right), Number(value)
    - Example: Add(Number(2), Multiply(Number(3), Number(4)))
  -  Concrete Syntax
    - Infix (common math): 2 + 3 * 4
    - Prefix (Lisp style): (+ 2 (* 3 4))
    - Postfix (RPN): 2 3 4 * +
```
- Validations & Constraints:
  - makes the model useful by validating the models
  - enforces constraints like invariants and cardinality 
  - metamodel + validations **not equal** to DSL
- Syntax
  - How users write or express programs
- Type System: "Static Correctness"
  - Rules that define what is valid in the language.
  - Prevents invalid combinations at design time
  - A strong DSL often encodes domain rules in its type system.
- Semantics (Meaning/Behavior):
  - implemented via (Code Generators + Interpreters + Execution Engine)
  - Semantics maps domain concepts to real-world effects
  - What the program does when it is executed

Glossary? No.
Structured Glossary? No.
Metamodel? No.
Metamodel + Validations? No.
Metamodel + Validations + Metamodel-specific Syntax? Yes!
Metamodel + Type System + Metamodel-specific Syntax + Execution Semantics? Double yes!
### DSL = domain abstraction + constraints + execution meaning

- The metamodel defines the domain concepts; syntax is just one way to represent them.
- Multiple concrete syntaxes can map to the same abstract syntax (metamodel).
- Not all domain rules fit cleanly into the type system — constraints handle the rest.
- DSLs often don’t execute directly — they transform into something else.
- Advanced DSLs invest heavily in tooling — that’s where productivity gains 
  come from.


```

A Logical View

           +----------------------+
           |     Semantics        |  → Meaning / execution
           +----------------------+
           |   Type System        |  → Static correctness
           +----------------------+
           |    Constraints       |  → Domain rules
           +----------------------+
           |     Metamodel        |  → Core concepts ⭐
           +----------------------+
           |  Abstract Syntax     |  → Structured form
           +----------------------+
           |  Concrete Syntax     |  → Text / UI
           +----------------------+
```

---

- Incidental Complexity: non-essential complexity by software design, developers 
often stemming from poor tool choices, over-engineering or rigid, inefficient
processes. This can be removed, simplified to align with KISS or Ocam's Razor.
Also, referred as accidental complexity from Fred Brooks article "No Silver 
Bullet".

- Essential Complexity: Inherently required in the system

https://www.cs.unc.edu/techreports/86-020.pdf
"I believe the hard part of building software to be the specification, design, 
and testing of this conceptual construct, not the labor of representing it and 
testing the fidelity of the representation. 

We still make syntax errors, to be sure; but they are fuzz compared to
the conceptual errors in most systems.

If this is true, building software will always be hard. 

There is. inherently no silver bullet."

https://www.linkedin.com/pulse/when-something-domain-specific-language-markus-voelter/
https://www.jetbrains.com/mps/concepts/domain-specific-languages/
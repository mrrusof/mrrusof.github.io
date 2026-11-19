---
layout: post
title: Multi-Pass Calculator
date: 2026-11-18
author: Ruslan Ledesma-Garza
summary: "A classic programming problem: parse and evaluate arithmetic
expressions.  We show how to do it with a multi-pass parser."
---

Make a program that evaluates arithmetic expressions like the
following.

- `(12 / 6) (2 * 3) + 3` $@\rightarrow@$ `15`
- `30 / 6 * (2 + 3) + 10` $@\rightarrow@$ `35`
- `((2 + 4) / 6) - 1` $@\rightarrow@$ `0`

We will follow some rules to make this task manageable.

- Numbers will always be integers.
- The value of expressions and subexpressions will always be integers
  and will always fit in signed 32 bit integers.
- `X ( Y )` means `X * Y`
- Operators `*` and `/` bind before `+` and `-`.
  - `1 + 2 * 3` -> `1 + (2 * 3)`
  - `1 * 2 + 3` -> `(1 * 2) + 3`
- Operators `*` and `/` (respectively `+` and `-`) associate to the
  left.
  - `1 + 2 - 3` -> `(1 + 2) - 3`
  - `4 / 2 * 3` -> `(4 / 2) * 3`

To make everything super clear, the formal grammar for the expressions
that your program will parse is the following.

```asciidoc
N:  integers
♦:  binary operators *, /, +, -
E:  expressions
E': expressions without unary minus at the beginning

E  ::=  N
       -N
       E♦E'
       E♦(E)
       (E)

E' ::= N
       E'♦E'
       E'♦(E)
```

**Input.** The input will be given to your program via stdin.  The
  input consists of a number `N` followed by `N` lines, each line
  may or may not consist of an expression. Example:

```asciidoc
6
(12 / 6  )  (2 * 3) + 3
30 / 6 * (2+ 3) + 10
((  2 + 4) / 6) -1
(1+2
110+*2
120*-2
```

**Output.** Your program will output its result via stdout.  The
  output consists of `N` lines, one for each input line consisting of
  the value of the expression, if any, or the text "parsing error" if
  the line is not an expression. Example:

```asciidoc
15
35
0
parsing error
parsing error
parsing error
```

# Multi-Pass Approach to Parsing

A common way of coding algorithms in the wild is to cram all steps and
aspects of the algorithm in a few functions with no clear structure,
so that a maximum level of job security can be achieved.  Rolling your
own parser could be one such exercise.  However, with the advent of
GenAI coding agents, this way of coding is no longer an effective
approach to job security (not that it ever was).  So, we must learn
the discipline of organizing our code and thoughts in a structured
way.

Enter the multi-pass approach to parsing.  We will iterate each input
expression several times so that with each pass we move one step
closer to extracting the structure of the expression.  As an example,
consider the input expression `12 + 23(-46) / 2 - 64`.

The first step is to tokenize the expression.  Tokenizing means
grouping characters of the input string for the expression into
indivisible values.  For example, `12` and `(` are tokens of the input
expression.  We create integer tokens like `12` by grouping adjacent
digits and we create the rest of the tokens by promoting individual
characters like `(` to tokens.  We ignore whitespaces and we raise an
exception if we find any character not in the grammar.  The
tokenization pass looks as follows.

```asciidoc
input expression:  12  +  23  (  -  46  )  /  2  -  64
          tokens:  12, +, 23, (, -, 46, ), /, 2, -, 64

```

Now we will build the structure of the input expression.  The
final structure of the input expression or syntax tree is the
following.

```asciidoc
      -
     / \
    /   64
   +
  / \
12   \
     (/)
    /   \
   *     2
  / \
 23  -
     |
     46
```

We will make the following passes over the tokens of the expression to
build the final syntax tree.

**1. Partial Syntax Tree Pass.** Convert each token into a syntax tree
   node and nothing more.  When we find an opening parenthesis, that
   signals a nested expression and we apply the whole procedure
   recursively to obtain the root node for the syntax tree of that
   nested expression, which we put next to the rest of the syntax tree
   nodes we collect in this pass.  The result is a sequence of syntax
   tree nodes.  For example, given the sequence of tokens for the
   input expression, the result of this pass is the following (we use
   `n(...)` to indicate the value of a syntax tree node).

```asciidoc
  expression:    12       +       23             (  -  46  )      /       2       -       64
      tokens:    12,      +,      23,            (, -, 46, ),     /,      2,      -,      64
partial pass:  n(12), n(_,+,_), n(23), n(_,*,_),  n(-,n(46)), n(_,/,_), n(2), n(_,-,_), n(64)
```

   In this partial pass, tokens `(, -, 46, )` become the final subtree
   `n(-,n(46))` because we apply all the passes recursively and put
   the node of the subtree in the sequence with the rest of the syntax
   tree nodes given by this pass.  Also note that in this pass we
   normalize the multiplication by parenthesis `23(-46)` by inserting
   a partial syntax tree for a multiplication `n(_,*,_)`.


**2. Unary Minus Pass.** We look at the beginning of the expression
   for a minus sign node and we apply it to the next syntax tree node.
   For the input expression, this pass does not change any nodes and
   the nodes stay as follows.

```asciidoc
  expression:    12       +       23             (  -  46  )      /       2       -       64
      tokens:    12,      +,      23,            (, -, 46, ),     /,      2,      -,      64
partial pass:  n(12), n(_,+,_), n(23), n(_,*,_),  n(-,n(46)), n(_,/,_), n(2), n(_,-,_), n(64)
 unary minus:  n(12), n(_,+,_), n(23), n(_,*,_),  n(-,n(46)), n(_,/,_), n(2), n(_,-,_), n(64)
```

   However, when we applied the passes to the subexpression `-46`, we
   proceeded in the following way.

```asciidoc
subexpression:   -       46
       tokens:   -,      46
 partial pass: n(-,_), n(46)
  unary minus: n(-,n(46))
```

**3. Multiplication and Division Pass.** Because multiplication and
   division bind before sum and subtraction, we go from left to right
   applying multiplication and division operators to their adjacent
   expressions.  The result is the following.

```asciidoc
  expression:    12       +       23             (  -  46  )      /       2       -       64
      tokens:    12,      +,      23,            (, -, 46, ),     /,      2,      -,      64
partial pass:  n(12), n(_,+,_), n(23), n(_,*,_),  n(-,n(46)), n(_,/,_), n(2), n(_,-,_), n(64)
 unary minus:  n(12), n(_,+,_), n(23), n(_,*,_),  n(-,n(46)), n(_,/,_), n(2), n(_,-,_), n(64)
    mult div:  n(12), n(_,+,_),              n(n(n(23),*,n(-,n(46))),/,n(2)), n(_,-,_), n(64)
```

  The construction of the subtree `n(n(n(23),*,n(-,n(46))),/,n(2))`
  happened from left to right as follows.

```asciidoc
n(12), n(_,+,_), n(23), n(_,*,_),  n(-,n(46)), n(_,/,_), n(2), n(_,-,_), n(64)
-->
n(12), n(_,+,_),         n(n(23),*,n(-,n(46))), n(_,/,_), n(2), n(_,-,_), n(64)
-->
n(12), n(_,+,_),              n(n(n(23),*,n(-,n(46))),/,n(2)), n(_,-,_), n(64)
```

**4. Sum and Subtraction Pass.** Same as with the previous pass but
   for sum and subtraction.  The result is the following

```asciidoc
  expression:    12       +       23             (  -  46  )      /       2       -       64
      tokens:    12,      +,      23,            (, -, 46, ),     /,      2,      -,      64
partial pass:  n(12), n(_,+,_), n(23), n(_,*,_),  n(-,n(46)), n(_,/,_), n(2), n(_,-,_), n(64)
 unary minus:  n(12), n(_,+,_), n(23), n(_,*,_),  n(-,n(46)), n(_,/,_), n(2), n(_,-,_), n(64)
    mult div:  n(12), n(_,+,_),              n(n(n(23),*,n(-,n(46))),/,n(2)), n(_,-,_), n(64)
     sum sub:                           n(n(n(12),+,n(n(n(23),*,n(-,n(46))),/,n(2))),-,n(64))
```
  
  The construction of the sum and subtraction happened from left to
  right like in the following diagram.

```asciidoc
n(12), n(_,+,_),              n(n(n(23),*,n(-,n(46))),/,n(2)), n(_,-,_), n(64)
-->
                   n(n(12),+,n(n(n(23),*,n(-,n(46))),/,n(2))), n(_,-,_), n(64)
-->
                         n(n(n(12),+,n(n(n(23),*,n(-,n(46))),/,n(2))),-,n(64))
```

The runtime for this approach is $@O(n)@$ on the length of the input
expression.  There are two reasons for this.

1. Although we do several passes over tokens and then partial syntax
 trees until we obtain the final syntax tree, we only touch each
 token/partial tree once in each pass and we do constant work for each
 token/tree we visit.
2. Subexpressions contribute a single root node and we do not walk again
 the tokens/trees for subexpressions.

Once we build the final syntax tree for the input expression, its
evaluation is a run-of-the-mill recursive function.


## Golang Implementation

We implement the multi-pass parsing approach in Golang as follows.  It
can be found
[here](https://github.com/mrrusof/algorithms/tree/master/calculator)
as well.

```golang
package main

import (
  "bufio"
  "errors"
  "fmt"
  "os"
  "strconv"
  "strings"
)

////////////////////////////////////////////////////////////
// Verbose

var verboseParsingErrors = false


////////////////////////////////////////////////////////////
// Debug

var debug = false

func Dprintf(format string, a ...any) (n int, err error) {
  if !debug {
    return 0, nil
  }

  return fmt.Printf(format, a...)
}

////////////////////////////////////////////////////////////
// Input reading

func readInput() []string {
  scanner := bufio.NewScanner(os.Stdin)

  scanner.Scan()
  n, _ := strconv.Atoi(scanner.Text())

  lines := make([]string, n)

  i := 0
  for scanner.Scan() {
    line := scanner.Text()
    lines[i] = line
    i++
  }

  return lines
}

////////////////////////////////////////////////////////////
// Tokenizing

const (
  TokenInt = iota
  TokenPlus
  TokenMinus
  TokenMult
  TokenDiv
  TokenLeftParen
  TokenRightParen
)

type Token struct {
  Type   int
  Value  string
}

func tokenize(expr string) ([]Token, error) {
  tokens := make([]Token, 0)

  runes := []rune(expr)

  for i := 0; i < len(runes); i++ {
    var token Token

    switch runes[i] {
    case '+':
      token.Type = TokenPlus
    case '-':
      token.Type = TokenMinus
    case '*':
      token.Type = TokenMult
    case '/':
      token.Type = TokenDiv
    case '(':
      token.Type = TokenLeftParen
    case ')':
      token.Type = TokenRightParen
    case '0', '1', '2', '3', '4', '5', '6', '7', '8', '9':
      var value strings.Builder

      IntTokenLoop: for ; i < len(runes); {
        switch runes[i] {
        case '0', '1', '2', '3', '4', '5', '6', '7', '8', '9':
          value.WriteRune(runes[i])
          i++
        default:
          i--
          break IntTokenLoop
        }
      }

      token.Type = TokenInt
      token.Value = value.String()
    case ' ':
      continue

    default:
      return nil, errors.New(
        fmt.Sprintf(
          "tokenize: Unexpected character found: %s --> %c <--",
          prettyTokens(tokens),
          runes[i]))
    }

    tokens = append(tokens, token)
  }

  return tokens, nil
}


////////////////////////////////////////////////////////////
// Pretty printing tokens

func prettyTokens(tokens []Token) string {
  var out strings.Builder

  for _, token := range tokens {
    switch token.Type {
    case TokenInt:
      out.WriteString(token.Value)
    case TokenPlus:
      out.WriteString("+")
    case TokenMinus:
      out.WriteString("-")
    case TokenMult:
      out.WriteString("*")
    case TokenDiv:
      out.WriteString("/")
    case TokenLeftParen:
      out.WriteString("(")
    case TokenRightParen:
      out.WriteString(")")
    }
    out.WriteString(" ")
  }

  return out.String()
}


////////////////////////////////////////////////////////////
// Construction of AST (parsing)

const (
  AstInt = iota
  AstPlus
  AstMinus
  AstMult
  AstDiv
  AstUnaryMinus
)

type Ast struct {
  Type       int
  IntValue   int
  Arg1       *Ast
  Arg2       *Ast
  TokenStart int
  TokenEnd   int
}

func makePartialAsts(tokens []Token, startIndex int) ([]Ast, int, error) {
  // Tokens to
  // 1. complete ASTs for operator arguments and
  // 2. partial ASTs for operators.

  if startIndex >= len(tokens) {
    return nil, 0, errors.New(
      fmt.Sprintf(
        "makePartialAsts: Expected a sub-expression but reached the end of the input: %s <HERE>",
        prettyTokens(tokens)))
  }

  var asts []Ast
  i := startIndex

  if tokens[i].Type == TokenMinus {
    ast := Ast {
      Type: AstUnaryMinus,
      TokenStart: i,
      TokenEnd: i + 1,
    }
    asts = append(asts, ast)

    i++
  }

  for ; i < len(tokens); i++ {
    switch tokens[i].Type {

    case TokenInt:
      intValue, _ := strconv.Atoi(tokens[i].Value)

      ast := Ast {
        Type: AstInt,
        IntValue: intValue,
        TokenStart: i,
        TokenEnd: i + 1,
      }

      asts = append(asts, ast)

    case TokenPlus, TokenMinus, TokenMult, TokenDiv:
      var tokenType int

      switch tokens[i].Type {
      case TokenPlus:
        tokenType = AstPlus
      case TokenMinus:
        tokenType = AstMinus
      case TokenMult:
        tokenType = AstMult
      case TokenDiv:
        tokenType = AstDiv
      }

      ast := Ast {
        Type: tokenType,
        TokenStart: i,
        TokenEnd: i + 1,
      }

      asts = append(asts, ast)

    case TokenLeftParen:

      if i > 0 {
        switch tokens[i - 1].Type {
        case TokenInt, TokenRightParen:
          astMult := Ast {
            Type: AstMult,
            TokenStart: i,
            TokenEnd: i + 1,
          }

          asts = append(asts, astMult)
        }
      }

      i++

      astSubExpr, endIndex, err := doParse(tokens, i)

      if err != nil {
        return nil, 0, err
      }

      i = endIndex

      if i >= len(tokens) {
        return nil, 0, errors.New(
          fmt.Sprintf(
            "makePartialAsts: Expected closing parenthesis at end of expression: %s <HERE>",
            prettyTokens(tokens)))
      }

      if tokens[i].Type != TokenRightParen {
        return nil, 0, errors.New(
          fmt.Sprintf(
            "makePartialAsts: Expected closing parenthesis: %s <HERE> %s",
            prettyTokens(tokens[:i]),
            prettyTokens(tokens[i:])))
      }

      asts = append(asts, astSubExpr)

    case TokenRightParen:
      return asts, i, nil
    }
  }



  return asts, i, nil
}

func makeUnaryMinusAst(asts []Ast) []Ast {
  if len(asts) < 2 {
    return asts
  }

  if asts[0].Type != AstUnaryMinus || asts[0].Arg1 != nil {
    return asts
  }

  var unaryMinusPass []Ast

  unaryMinusAst := Ast {
    Type: AstUnaryMinus,
    Arg1: &asts[1],
    TokenStart: asts[0].TokenStart,
    TokenEnd: asts[1].TokenEnd,
  }

  unaryMinusPass = append(unaryMinusPass, unaryMinusAst)

  for i := 2; i < len(asts); i++ {
    unaryMinusPass = append(unaryMinusPass, asts[i])
  }

  return unaryMinusPass
}

func makeMultDivAsts(asts []Ast, tokens []Token) ([]Ast, error) {
  var multDivPass []Ast

  for i := 0; i < len(asts); i++ {
    switch asts[i].Type {

    case AstMult, AstDiv:
      if asts[i].Arg1 != nil {
        multDivPass = append(multDivPass, asts[i])
        continue
      }

      if i == 0 {
        return nil, errors.New(
          fmt.Sprintf(
            "makeMultDivAsts: Expected first argument for multiplication or division: %s <HERE> %s",
            prettyTokens(tokens[:asts[i].TokenStart]),
            prettyTokens(tokens[asts[i].TokenStart:])))
      }

      if i + 1 == len(asts) {
        return nil, errors.New(
          fmt.Sprintf(
            "makeMultDivAsts: Expected second argument for multiplication or division: %s <HERE> %s",
            prettyTokens(tokens[:asts[i].TokenEnd]),
            prettyTokens(tokens[asts[i].TokenEnd:])))
      }

      if asts[i - 1].Type != AstInt && asts[i - 1].Arg1 == nil {
        return nil, errors.New(
          fmt.Sprintf(
            "makeMultDivAsts: Expected well-formed expression as first parameter of multiplication or division: %s --> %s <-- %s",
            prettyTokens(tokens[:asts[i - 1].TokenStart]),
            prettyTokens(tokens[asts[i - 1].TokenStart:asts[i - 1].TokenEnd]),
            prettyTokens(tokens[asts[i - 1].TokenEnd:])))
      }

      if asts[i + 1].Type != AstInt && asts[i + 1].Arg1 == nil {
        return nil, errors.New(
          fmt.Sprintf(
            "makeMultDivAsts: Expected well-formed expression as second parameter of multiplication or division: %s --> %s <-- %s",
            prettyTokens(tokens[:asts[i + 1].TokenStart]),
            prettyTokens(tokens[asts[i + 1].TokenStart:asts[i + 1].TokenEnd]),
            prettyTokens(tokens[asts[i + 1].TokenEnd:])))
      }

      multDivAst := asts[i]
      arg1 := multDivPass[len(multDivPass) - 1]
      arg2 := asts[i + 1]
      multDivAst.Arg1 = &arg1
      multDivAst.Arg2 = &arg2
      multDivAst.TokenStart = arg1.TokenStart
      multDivAst.TokenEnd = arg2.TokenEnd

      multDivPass[len(multDivPass) - 1] = multDivAst

      i++

    default:
      multDivPass = append(multDivPass, asts[i])
    }
  }

  return multDivPass, nil
}

func makeSumSubAsts(asts []Ast, tokens []Token) ([]Ast, error) {
  var sumSubPass []Ast

  for i := 0; i < len(asts); i++ {
    switch asts[i].Type {

    case AstPlus, AstMinus:
      if asts[i].Arg1 != nil {
        sumSubPass = append(sumSubPass, asts[i])
        continue
      }

      if i == 0 {
        return nil, errors.New(
          fmt.Sprintf(
            "makeSumSubAsts: Expected first argument for sum or subtraction: %s <HERE> %s",
            prettyTokens(tokens[:asts[i].TokenStart]),
            prettyTokens(tokens[asts[i].TokenStart:])))            
      }
      
      if i + 1 == len(asts) {
        return nil, errors.New(
          fmt.Sprintf(
            "makeSumSubAsts: Expected second argument for sum or subtraction: %s <HERE> %s",
            prettyTokens(tokens[:asts[i].TokenEnd]),
            prettyTokens(tokens[asts[i].TokenEnd:])))
      }

      if asts[i - 1].Type != AstInt && asts[i - 1].Arg1 == nil {
        return nil, errors.New(
          fmt.Sprintf(
            "makeMultDivAsts: Expected well-formed expression as first parameter of multiplication or division: %s --> %s <-- %s",
            prettyTokens(tokens[:asts[i - 1].TokenStart]),
            prettyTokens(tokens[asts[i - 1].TokenStart:asts[i - 1].TokenEnd]),
            prettyTokens(tokens[asts[i - 1].TokenEnd:])))
      }

      if asts[i + 1].Type != AstInt && asts[i + 1].Arg1 == nil {
        return nil, errors.New(
          fmt.Sprintf(
            "makeMultDivAsts: Expected well-formed expression as second parameter of multiplication or division: %s --> %s <-- %s",
            prettyTokens(tokens[:asts[i + 1].TokenStart]),
            prettyTokens(tokens[asts[i + 1].TokenStart:asts[i + 1].TokenEnd]),
            prettyTokens(tokens[asts[i + 1].TokenEnd:])))
      }

      sumSubAst := asts[i]
      arg1 := sumSubPass[len(sumSubPass) - 1]
      arg2 := asts[i + 1]
      sumSubAst.Arg1 = &arg1
      sumSubAst.Arg2 = &arg2
      sumSubAst.TokenStart = arg1.TokenStart
      sumSubAst.TokenEnd = arg2.TokenEnd

      sumSubPass[len(sumSubPass) - 1] = sumSubAst

      i++

    default:
      sumSubPass = append(sumSubPass, asts[i])
    }
  }

  return sumSubPass, nil
}

func doParse(tokens []Token, startIndex int) (Ast, int, error) {
  partialAsts, endIndex, errPartial := makePartialAsts(tokens, startIndex)

  if errPartial != nil {
    return Ast{}, 0, errPartial
  }

  unaryMinusPass := makeUnaryMinusAst(partialAsts)
  multDivPass, errMult := makeMultDivAsts(unaryMinusPass, tokens)

  if errMult != nil {
    return Ast{}, 0, errMult
  }

  sumSubPass, errSum := makeSumSubAsts(multDivPass, tokens)

  if errSum != nil {
    return Ast{}, 0, errSum
  }

  if len(sumSubPass) != 1 {
    return Ast{}, 0, errors.New(
      fmt.Sprintf(
        "doParse: Expected exactly one AST for tokens: %s --> %s <-- %s",
        prettyTokens(tokens[:startIndex]),
        prettyTokens(tokens[startIndex:endIndex]),
        prettyTokens(tokens[endIndex:])))
  }

  ast := sumSubPass[0]

  return ast, endIndex, nil
}

func parse(tokens []Token) (Ast, error) {
  ast, tokenCount, err := doParse(tokens, 0)

  if err != nil {
    return Ast{}, err
  }

  if tokenCount < len(tokens) {
    return Ast{}, errors.New(
      fmt.Sprintf(
        "parse: Tokens left unparsed: %s --> %s\n",
        prettyTokens(tokens[:tokenCount]),
        prettyTokens(tokens[tokenCount:])))
  }

  return ast, nil
}


////////////////////////////////////////////////////////////
// Pretty printing AST

func doPrettyAst(ast Ast, out *strings.Builder) {
  switch ast.Type {
  case AstInt:
    out.WriteString(strconv.Itoa(ast.IntValue))
  case AstPlus, AstMinus, AstMult, AstDiv:
    out.WriteString("(")
    doPrettyAst(*ast.Arg1, out)
    out.WriteString(") ")

    switch ast.Type {
    case AstPlus:
      out.WriteString("+")
    case AstMinus:
      out.WriteString("-")
    case AstMult:
      out.WriteString("*")
    case AstDiv:
      out.WriteString("/")
    }
    
    out.WriteString(" (")
    doPrettyAst(*ast.Arg2, out)
    out.WriteString(")")
  case AstUnaryMinus:
    out.WriteString("-(")
    doPrettyAst(*ast.Arg1, out)
    out.WriteString(")")
  }
}

func prettyAst(ast Ast) string {
  var out strings.Builder
  
  doPrettyAst(ast, &out)

  return out.String()
}


////////////////////////////////////////////////////////////
// Evaluation of AST

func evaluate(ast Ast) int {
  var value int

  switch ast.Type {
  case AstInt:
    value = ast.IntValue
  case AstUnaryMinus:
    value = - evaluate(*ast.Arg1)
  case AstPlus:
    value = evaluate(*ast.Arg1) + evaluate(*ast.Arg2)
  case AstMinus:
    value = evaluate(*ast.Arg1) - evaluate(*ast.Arg2)
  case AstMult:
    value = evaluate(*ast.Arg1) * evaluate(*ast.Arg2)
  case AstDiv:
    value = evaluate(*ast.Arg1) / evaluate(*ast.Arg2)
  }

  return value
}


////////////////////////////////////////////////////////////
// Parsing + evaluation workflow

func calculate(expr string) (int, error) {
  tokens, errTokens := tokenize(expr)

  if errTokens != nil {
    return 0, errTokens
  }

  Dprintf("tokens = %s\n", prettyTokens(tokens))

  ast, errAst := parse(tokens)

  if errAst != nil {
    return 0, errAst
  }

  Dprintf("ast = %s\n", prettyAst(ast))

  return evaluate(ast), nil
}

func main() {
  lines := readInput()

  for _, line := range lines {
    Dprintf("line = %s\n", line)

    value, err := calculate(line)

    if err != nil {
      if verboseParsingErrors {
        fmt.Println(err)
      } else {
        fmt.Println("parsing error")
      }
    } else {
      if debug {
        Dprintf("value = %d\n", value)
      } else {
        fmt.Printf("%d\n", value)
      }
    }
  }
}
```

{% include share.html %}


{% include comments.html %}

# Ast Pattern Matching


    matchAst(arg, matchErrors):
    of nnkStmtList(
      _,
      _,
      nnkForStmt(
        nnkIdent("i"),
        nnkInfix,
        `mysym` @ nnkStmtList
      )
    ):
      echo "The AST did match!!!"
      echo "The matched sub tree is the following:"
      echo mysym.lispRepr
    else:
      echo matchErrors
      echo "sadly the AST did not match :("


`matchAst` is where the magic happens. In the of-branch is a tree that
can at any time be generated vial `lispRepr` on an arbitrary
`NimNode`. But the pattern language also allows for some additional
constructs, to winden the possibilities of the pattern.


The grammar for the pattern matching looks like this:

  * ``<pattern>`` is an arbitrary pattern

  * ``of <pattern>:`` is a branch of the pattern matching library.

  * ``nnkInfix`` an example of just the node kind without braces. in this
    case the childern of the infix node are not checked.

  * ``nnkStmtList(<pattern>, <pattern>, <pattern>)`` will match on any
    any node of kind `nnkStmtList` and length 3.

  * ``nnkStmtList`` without an opening brace `(` matches nodes of kind
    nnkStmtList.  It will check for length of any children.

  * ``nnkStmtList()`` matches only an empty statement list.

  * `_` the wildcard, matches on everything
  .
  * `` `somename` `` is a named subtree. Like the wildcard, it matches
    on everything. It creates an identifier for that subtree, so that
    it has a name in the branch body.

  * `` `somename` @ <pattern> `` a named subtree. it matches on
    _<pattern>_ but binds the name `somename` to that node.
    checked by _<pattern>_

  * ``else:`` the else branch is the code path that is taken when the
    pattern fails to match. The else branch is optional. When a
    pattern does not match, an error will be raised.

  * `matchErrors` in the example above is used as an identifier for a
    seq of match errors. it will be avalilable in the else branch as
    ``let matchErrors: seq[MatchingError]``. It's an optional argument
    and the purpose is to debug matching expressions.


    matchAst(arg, matchErrors):
    of <pattern>: # branch A
      discard
    of <pattern>: # branch B
      discard
    of <pattern>: # branch C
      discard
    else:
      echo "branch A could not match because:"
      echo matchErrors[0]
      echo "branch B could not match because:"
      echo matchErrors[1]
      echo "branch C could not match because:"
      echo matchErrors[2]

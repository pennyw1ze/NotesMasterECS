COMPLICATIONS
- **Concurrency**: even though the actual program might be satisfying some assertion, this might be invalidated by a concurrent program which is modifying the same variable.

#### VCGen
A standard approach to program verification is to use Verification Condition Generator, or VCGen. Using annotations in the program, VCGen produces a set of logical properties, called verification conditions.
If the verification conditions are true, the annotations are correct and the program satisfies the specifications.
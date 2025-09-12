## ZaidaTek Style Reference
    - version: 250912
    - author: Andreas 'cr4q(sh0t)' Riebesehl
    - license: subject to further notice, "philosophically CC-BY-SA"

### General Notes
    - the reference language for the derival of general style is C
    - some/most conventions (e.g. MACRO_CASE macro-variables) are copied
    - choose '[UNKNOWN, presumed existing] style' with regards to naming precendence
        - prefer TAG_[DOMAIN][METHOD]() rathen than (English) widespread TAG_[METHOD][DOMAIN]()
        - e.g. TAG_ReadFile(), TAG_WriteFile() should be TAG_FileRead(), TAG_FileWrite()
        - this makes it easier to search and replace more contextually related text-snippets in files
    - due to using variable-prefixing, aRelevantCapitalization is accepted as a trade-off in enhancing readability
    - PascalCaseIsUsed_ButSnakeUnderlining_RelevantContext_Part_0 is fine (_Part0 is also fine)
    - due to heavy reliance on both 'l' and 'i' as variable prefixes
        - a font that makes them easily discernbile is recommended
        - otherwise, 'i' should be replaced by 'z', as it the less common prefix in existing code
    - in doubt: prefer more braces to make precedence clear
    - avoid skipping (any) optional curly braces following if, else, for, while, switch, etc.
    - (modern) horizontal space can and should be used
        - for (int i = 0; i < 100; ++i) {oData[i] = gFunction(i); lResult |= oData[i]; if (lResult == 0xff) {break;}}
        - is a completely acceptable one-liner
    - even lines spanning one-to-three extra screen widths can be deemed acceptable if
        - its content can be inferred (e.g. a thousand-element-LUT defined in a single line)
        - it does not interfere (make scrollbar too small) in otherwise (rarely) required horizontal scrolling,
        - e.g. code one-liners spanning two screens should avoid having thousand-element-LUTs (or vice-versa)

### Variable, Notes
    - avoid single-letter variables, use only with extremely limited and short scope
        - also only use easily inferred context for single-letters,
        - e.g. i: iterator, x,y,z: coordinates, r,g,b,a: color values
        - do NOT (never) use single-letter variables for pointers
            - void *lContext is preferred
            - void *ptr is an acceptable (temporary) name
    - choose to be more verbose rather than not
    - avoid (contrived) abbreviations as much as possible
    - virtually all variables are prefixed, usually with a single-letter
        - to reduce naming collisions
        - increase semantic legibility
        - determine variable scope from name alone
    - prefixes are always lower-case, even in #define mFUNCTION(iINPUT, oOUTPUT)
    - single-letter variables are (inherently?) exempt from prefixing-(but not nesting)-guidelines
    - functions are variables, that are functions; in this sense, they are not exempt from prefixing
        - unless there is a global 'TAG_', in which case, global functions are 'TAG_FunctionName()'
    - the 'r' prefix is only to be used internally
        - implying it is only used inside libraries for variables that are not accessible from outside the library
    - the 'm' prefix is also only to be used internally for macro-functions
        - 'pseudo-functions' (i.e. macro-functions meant to look like real functions) should emulate their counterparts instead
        - there are conflicting style-implementations of macros regarding their capitalization, it is yet to be defined
    - the 'a' prefix is only to be used in ASM contexts

### Variable, Prefixes
    - variables are prefixed, usually with a single-letter
    - these are COMPLETELY UNRELATED to the concepts in Hungarian Notation
        - as prefixes should indicate scope and provide semantic meaning, not type inference
        - and, rather, it is unfortunate variable-prefixes are often conflated with Hungarian Notation or vice-versa
    - the following is a table of commonly used prefixes, in the format prefix:mnemonic:domain:example
        g   global      scope   char gStackBuffer[2048]; // variable accessible from everywhere
        l   local       scope   int lContinue = 0x1; // variable accessible only within scope
        r   runtime     scope   int rPrivate = 0x1; // usually only found inside libraries, with scope limited to it
        m   macro       scope   #define mFUNCTION(iPARAM, oTARGET) ({...}) // shall never leave a dangling #undef
        a   asm         scope   "lds r25, %[aIN]\nsts %[aOUT], r25\nreti\n" :: [aIN]"i"(&gSource), [aOUT]"i"(&gTarget)
        i   input       func    int gCRC32(const void *iData); // func params that are only read
        o   output      func    void gGetTimeNow(int *oTime); // func params that only are written to
        io  in/out      func    void gCheckAndCoerce(void *ioData); // func params both read and written to
    - pointer-typed *iInputVariable should always only be read from, and also be defined "const"
    - pointer-typed *oTargetVariable should always only be written to
    - pointer-typed *ioVariable should be used when it is both a target to read from and to write to
    - it is acceptable (at programmer discretion) to modify non-pointer iInput variables inside their functions
        - though it should be avoided, e.g. "int lInput = iInput; lInput += ..." is preferred,
        - unless, usually in tighter functions, such a redeclaration is (not just ostensibly) redundant,
        - e.g. iInput is never needed in its original form anymore (which can reasonably be inferred without prior knowledge)
    - thus, according to the presented style-guidelines, it must be
        - "*oData = lResult" or "return lResult" are common scenarios
        - "return oTarget" is not an impossible scenario (e.g. a copy function also returning its target pointer)
        - "int oResult" declared inside a function is illegal, as it MUST be declared "int lResult" instead

### Variable, Nesting
    void gFunction(void *iData) {
        void lFunction(void *iiData) {
            void llFunction(void *iiiData) {
                int lllVariable;
                for (int i = 0x1; i; i <<= 1) {for (int ii = 0x1; ii != i; ii <<= 1) {/*...*/}}
            }
            int llVariable;
            for (int i = 0; i <= 0xff; ++i) {for (int ii = 0; ii < i; ++ii) {/*...*/}}
        }
        int lVariable;
        for (int i = 0; i < 100; ++i) {for (int ii = 0; ii < i; ++ii) {/*...*/}}
    }

### Numerals, Integer
	// Prefer using unsigned whenever possible
	// as it is fully well-defined, especially/particularly overflow behaviour
	
	unsigned int lCount = 16;
    // decimal notation when variable is used for its quantative/scalar value
	
    unsigned int lBits = 0x10;
	// hexadecimal notation when variable is used for its binary bit properties
	
	// Notable exceptions to the above are
	// e.g. when a hexadecimal value is used as a scalar value,
	// but retains its significance, due to its hexadecimal value
	// (see nesting example above, 2nd-level for-loop)

### Numerals, Float
	float lFloat = 8192.0;
	// always place the optional decimal and zero as well, regardless of (in)significance


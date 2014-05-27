ScrambleSearch
======

##  Description
Scramble with Friends™ is a word game played on a 4x4 grid of letters.  The player forms words from letters, and may only use each tile once per word.  The word score is computed from three sources:  the summation tiles' letter, a bonus for words atleast five letters, and special tiles that add multipliers to the letter or word.

## Search
This programs reads the dictionary file [enable1.txt](http://dotnetperls-controls.googlecode.com/files/enable1.txt), and maintains a HashSet for fast lookup.  The board is read from the standard input horizontally, accepting the first 16 letters.  To denote a tile that is a double or triple letter bonus, immediately append _+2_ or _+3_ respectively.  Similiarly, _*2_ and _*3_ represents double and triple word.

A player may start constructing a word from any `Tile`.  All initial starting `Location`s are inserted into a `fringe` queue.

```
        for(int i = 0; i < MAX_BOARD_WIDTH; ++i)
            for(int j = 0; j < MAX_BOARD_HEIGHT; ++j)
                fringe.add(new State(Scramble.board,new Location(j,i)));
```
To form words, the player constructs words from adjacent letters.  `State` is kept that maintains the `Location` of the current `Tile`, the word constructed so far, the word score, the available `Action`s.  An `Action` is the cardinal direction of the adjacent `Tile` from the `State`'s current `Tile`.  The available actions from a given `State` are constrainted by the current and prior `Location`s.  That is to say, that no `Action` would be available to a `State` that would result in an invalid `Location` outside the board, or select a `Tile` that has already been selected in the sequence of `State`s the preceeded.

The next `State` is one that has advanced the current `Location` by an `Action`.  For each available `Action` to a `State`, a new `State` is created and added back to the `fringe` queue.

```
        void find_words()
        {
            State s;
            WordList found = new WordList();
            while (( s = fringe.poll()) != null)
            {

                String w = s.getWord();
                if(dict.isWord(w))
                    found.add(w,s.getScore());

                LinkedList<State> next_states = new LinkedList<State>();

                for(Action a : s.available_actions())
                    next_states.add(s.next(a));

                for(State ns : next_states)
                    fringe.push(ns);
            }
	    ...
```

The `State` also maintains the score of the tiles used to construct the words.  The `WordList` containing the matching words is a priority queue sorted by score.



### Environment Variables

__MIN_WORD_SIZE__ 		Words less than this value are not reported.

__MAX_WORD_SIZE__ 		States that construct words longer than this value are not explored.

__SCORE__ 		displays next to the word its score


### Example


```
$ javac src/ScrambleSearchApp.java
$ MIN_WORD_SIZE=8 java -cp src/ ScrambleSearchApp <<EOF
c*2eybh+2nsee+3a+3rddteu
EOF

Board:
c*2	e	y	b	
h+2	n	s	e	
e+3	a+3	r	d	
d	t	e	u	

detrudes
durances
enchaser
tranches
enchased
cheaters
Found: 6

```



Scramble with Friends™ is a registered trademark of Zynga, for which this has no affiliation.

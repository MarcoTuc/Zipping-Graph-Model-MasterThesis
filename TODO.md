#######################################################
#### Simulation Exploration 

-   Decreasing minimum-nucleation has the effect of lowering average resulting rates for strands.
    I saw this by confronting 1 vs 3 minimum nucleation. 
    Since my model is already under-computing rates for minimum nucleation of 3, I will need to 
    do a new simulation with some higher minimum nucleation value and see how better it behaves. 

-   Try one simulation with Geometry(360,360) and see how it changes results. 

-   There are some strands that systematically get over/under evaluated.
    I need to check in detail what's happening in there, if there are any shitty dynamics taking place. 

#######################################################
#### About the simulator

!) Implement the secondary structure dependance 
    - implement it in Strand class
    - implement it upwards to: 
        -- Complex 
        -- Chamber 
        -- Kinetwork 
        -- Simulator

!!) Implement a method inside the Kinetwork class 
    to help produce visualizations of the kinetwork
    (see helpernotes4 for how to implement it)
    - get some coloring stuff to divide 
        -- offnucleations - blue
        -- slidings       - dark blue 
        -- onnucleations  - green
        -- zippings       - dark green
        -- singlestranded - yellow
        -- duplexed       - red 

# !!!!) Dimensional analysis of constants and formulas inside (DONE A FIRST CHECK, DO A SECOND CHECK IF NEEDED)
        Kinetics class 
            -   there's clearlys some wrong shit as one can see
                by the orders of magnitude you get for diffusion



# !!!) Implement the Simulator from the Kinetwork 
    - Translate simhelper1.ipynb code into the class 


#######################################################
#### Experimental verification

!) Make a routine for simulating the model over hertel's data 


#######################################################
#### Notes on how to improve the model on a conceptual level

#       Off-register nonzipping
!!!!)   By looking at off-register nucleations we can see that there are direct nucleations
        going on directly from singlestranded to lots of base-pairs.
        This entails that off-register nucleations will have a profound impact on the dynamics
        since there are very short pathways connecting single stranded to duplex like: 
        (example is made with few nucleotides long strands as an example)
        (say that allowed minimum nucleation is 3 base pairs long and we have octamers)


        the following pathway is possible:

        ........+........
        .(((((((+.)))))))
        ((((((((+))))))))

        which is unrealistic. 

###     - How to solve this: 
            1\  Cut off-register nucleations with a number of base pairs longer than 
                some value plus the minimum nucleation length. Add some value because otherwise
                we're limiting the trajectory too much. 
            2\  When off-register collisions entailing the presence of more than minimum nucleation
                number of base pairs happen, model them as:
#                          ss + ss --> off-nucleation --> zipping --> sliding --> duplex 
        
            I think that solution 2 is the physically realistic one but it needs me to do some 
            quite annoying tweaks to all the classes Strand, Complex and Chamber
            Solution 1 on the other hand is kinda physically unrealistic but at least it is easy
            to implement since it will just mean to cut some edges out of the kinetwork graph 

###     - what to do about it
            1\  I will first continue implementing this stuff as it is with the short pathway 
                and see what happens to the results
            2\  Then I will first try the solution number 1 and check experiments
            3\  If I'll have time left I will go for solution number 2 
                to make things more realistic (hopefully more accurate)

#       Nonsense inside slidings thermodynamics!
!!!!)   Basal sliding rate is 2e7.
        What can happen is that the free energy difference between two slidings is positive 
        such that the fws is 2e7 and the bwd is some orders of magnitude over 2e7 (say 2e9).
        How do I fix this? 

#       Wrong zipping sides: 
!!!!)   Some forward zippings are actually referring to backward zippings. 
        Fix this (see wrongdoingszippingline64.html line 64)
        In particular it is zipping_55 that has fwd and bwd switched
#       By looking at new trajectory plots I see that this bug is happening quite often. 

         


######################################################
### THEORETICAL CONSIDERATIONS: 

<Non-Markovianity>
Right now the kinetic network we gillespie simulate is a markovian one. 
We are not inferring transitions probabilities from any other information than the current state. 
Actually this is not the case as the actual physical location of strands will influence the probability
of having this or that subsequent transition. For example let's think about a sliding going into zipping: 
'........(.+........).' sliding
'..........+..........' singlestranded
'(.........+.........)' nucleation
The sliding in this case is one like this: AAAAAAAAAG
                                                   TCTTTTTTTT 
Then it simplexes: 
Then it nucleates like: AAAAAAAAAG
                        TCTTTTTTTT
                        ^
It is clear that here there's no information about spatial permanence of objects 
which will eventually influence markovianity rendering the process actually dependant
on current state + state previous to that (it is just order 1 nonmarkovian)


#### #################################################
#### ################# DONE ##########################
#### #################################################


# DONE) Add kinetic constants to edge creation routines in Kinetwork class
        we have the following forward rates:
            - nucleations
                this gets divided by the total number of off and on register nucleations possible 
            - sliding
                for now this is a constant value but I want to make it 
                dependant on the "separation" between one sliding and 
                the next but I don't really know how atm. 
            - zipping
                for now this is just going to be the diffusion limited rate
                without any geometric constraints
    
    
CSE 535 Course Project: Implementation of Robert van Renesse Multi Paxos with algorithmic and code optimizations in DistAlgo
Due date: 12/5/2013
Submit date:10/6/2013
by Madhurima Roy (SBUID 108997204)
Credits: 3
--------------------------------------------------------------------------------------------------------------------------
ATTACHMENTS
------------

- vrpaxos-distalgo-optim-roy_code-optimized.da

- README.txt

- readings of optimized code.txt

- readings of original code.txt
------------------------------------------------------------------------------------------------------------------------
GENERAL USAGE NOTES
--------------------

- How to run:
python -m distalgo.compiler vrpaxos-distalgo-optim-roy_code-optimized.da
python -m distalgo.runtime vrpaxos-distalgo-optim-roy_code-optimized.da

- The code has been developed and tested in python 3.3.2 on Linux

-------------------------------------------------------------------------------------------------------------------------
OPTIMIZATION SUMMARY
--------------------

Algorithmic optimization:
	- Leader keeps a track of which slots have been decided. For P1a request, leader includes the first available slot which doesn’t have a decision. Upon active, leader doesn’t need to start commanders for slots that have decisions and it doesn’t need to maintain proposals for these slots.
	- Requests maintained by Replicas should contain only those requests for slot# higher than slot_num
	- Acceptors maintain only recent accepted pval for each slot return only these pvals in P1b message to Scout
	- Replicas let leader know when a command is performed, then leaders can notify acceptors to release corresponding state. State of acceptor contains slot_num. When state is released, all pvals lower than this slot_num should be deleted.
	- Keep state of acceptors and leaders on disk so that on crash, they can recover from disk.
	- Failure detection: to associate a time_out with each ballot for a leader. When ballot gets pre-empted, next ballot uses time_out that is X 2 (multiplied by 2).
	
Code optimization:
In the efficient versions of the above algorithms we are trying to optimize the code by using 
	- counts instead of whole lists
	- using for loops and breaking midway to prevent unneccessary iteration through the loops
	- removing certain set queries and list comprehensions and replacing with ifs and loops to reduce the performance load

---------------------------------------------------------------------------------------------------------------------------
PERFORMANCE:
-----------------
Statistics: Clients = 5, Replicas = 10, Leaders = 5, Acceptors = 20
 
The response time is observed for set of 25 requests at a time because that’s what a round would comprise of. Each of the 5 Client, sends 1 request to 5 Replicas which makes 25 requests in one round.

Start time and end time of experiment:
For state of art: 23:43 to 00:15 (In 33 mins 36 responses received)
For optimized: 17:09 to 17:18 (In 9 mins 400 responses received)

Response for each round of requests | van Rennesse DistAlgo implementation (secs) | Optimized van Rennesse DistAlgo implementation (secs)
------------------------------------+---------------------------------------------+------------------------------------------------------
                                  	Min 	 |	Max	 |  	Avg       |	Min	|	Max 	|	Avg
-------------------------------------------------+---------------+----------------+-------------+---------------+------------------------
		1 - 25		    |	0.870553 | 381.928459	 | 102.8757153    |  0.56908    |     19.72433  |	5.61058912
-------------------------------------------------+---------------+----------------+-------------+---------------+------------------------
                26 - 50	            |  118.61737 |  382.742646   |  214.880414    |  1.17464    |     19.21194  |       6.77098356
-----------------------------------------------------------------------------------------------------------------------------------------

The optimized code runs seamlessly through-out the experiment. Even for the requests in the range of 1000, the delay was not more than 30 sec.
However the current state of art takes a long time to proceed after 6-8 response.


---------------------------------------------------------------------------------------------------------------------------------------
REFERENCE:
-----------
[1] Leslie Lamport, The Part-Time Parliament. Research Paper  
http://research.microsoft.com/en-us/um/people/lamport/pubs/lamport-paxos.pdf
[2] Robbert van Renesse, Paxos Made Moderately Complex. Research Paper. From course web page 
http://www.cs.stonybrook.edu/~liu/cse535/vanRenesse-paxos-120429.pdf
[3] Bo Lin, Implementation of Multi Paxos in DistAlgo. From course web page 
http://www.cs.stonybrook.edu/~liu/distalgo/vrpaxos-distalgo-bolin.da 
[4] Wikipedia, version 5 November 2013 at 23:27
http://en.wikipedia.org/wiki/Paxos_%28computer_science%29
[5] Will Portnoy, Lessons Learned from Implementing Paxos. Blog of a Microsoft employee, June 14, 2012
http://blog.willportnoy.com/2012/06/lessons-learned-from-paxos.html
[6] Marco Primi, Implementing a high throughput Atomic Braodcast, a Master’s Thesis from University of Lugano, May 2009 http://www.inf.usi.ch/faculty/pedone/MScThesis/marco.pdf
[7] Leslie Lamport. Fast Paxos.  Distributed Computing, October 2006, Volume 19, Issue 2. pp 79-103
http://research.microsoft.com/pubs/64624/tr-2005-112.pdf
-----------------------------------------------------------------------------------------------------------------------------------------



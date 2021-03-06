# CatForce
GOL Catalyst search utility based on LifeAPI library. 

The main advantage of CatForce is that it doesn't make any assumptions about the nature of the interaction. As long as the catalysts are back in place in given number of generations, they all could be destroyed and reapear several times. 

It uses brute force search in some area instead of tree search (yet several optimizations were added to make sure it works fast). 

Another major advantage of this approach is parallelization (which is much harder to implement in tree depth search). 

**NOTE** As LifeAPI CatForce as well is working on 64x64 torus, and uses 64 longs to contain state. So it will works twice faster on 64 bit machines. The torus center is (0, 0) and left upper corner is (-32, -32) and lower right corner is (31,31). It has the same Y axis as golly (y up is negative). X axis is as usual (left negative rigth positive). 

Another feature of LifeAPI preserved in CatForce is eliminating edge gliders. LifeAPI is currently "listening" on the edges of the torus for gliders and removes them. This feature is also true for CatForce. There is an option to access the eliminated glider information, like generation and location, but it seems no one is actually interested in this information at this stage. 

Usage
--

Pass the .in to the CatForce.exe. 

Command Line: CatForce.exe 1.in

Technical Details
--

**.in file format** 

Please see 1.in in the repository for correct usage. 

Notice the delimeter is " " - i.e. space. 
Don't place "" for strings. Just use them with space delimeter. 
FileOutput can use spaces in the name - but this is not recommended. 

---

`max-gen`

CatForce will never iterate after max-gen. 

`start-gen`

The first gen encounter is allowed. 

**NOTE** Encounter - defined as active cell from the input pattern is present in the immediate neighborhood of the catalyst.

`last-gen`

The last gen to wait for some encounter. If no encounter had occured after last-gen the search will move on. 

`num-catalyst`

The number of catalyst to place (note that if you chose 3 catalyst all three of the SLs should be catalysts). 

`stable-interval`

For how long all the catalysts should remain untouched (after activation) to be considered stable. 

`search-area x y w h`

Search area is always rectangular. 

(x, y) is the starting point (w, h) - width height. 

`pat rle dx dy`

The main pattern rle. 
(dx, dy) - optional. Transformation for the pat. 

`cat <rle> <max-gen-petrube> dx dy <symmetries-char> [forbidden <rle> x y]`

catalysts - can be few in a single .in file. 

rle - the rle of the catalyst    
max-gen-petrube - the maximal generations that a pattern can be idle. Transparent catalysts might be idle for longer periods.    
(dx, dy) - Mandatory parameters to define the center of the catalyst.    
symmetries-char - character that defines all symmetries the catalyst can be used. Currently supports:    
| + / x * -

-  |   mirror by y.
-  -   mirror by x.
-  +   mirror by x, y and both. 
-  /  diagonal mirror.
-  x  make for 180 degree symmetrical catalysts.
-  *  All 8 variations of transformations. 

forbidden is an option that will search for rle in the same location of the catalyst. If forbidden is matched, in any iteration of the potential solution (and on the place of the catalyst), the solution is ommited from the report. You can see forbidden as "catalyst attached filter", i.e. the forbidden pattern is based on catalyst location. 

Made to exclude eaters and similiar unwanted "grabage". You can use several forbidden rules. See 3.in for example. 

`output <file.rle>`

output file name. 

`filter gen rle dx dy`

Note: one can use several filters. The filter will be checked if successful catalyst was found. 

Note: filter will assume live cells in rle and dead cell in close proximity neighborhood to the pattern in rle. 

gen - the generation at which the filter is applied. 

rle - the pattern rle that should be present. 

(dx dy) - transform of the pattern (mandatory). 

`filter min-max rle dx dy`

min-max gen range added to filter. This filter is an option to have the rle in range of generations. If in all the range the required rle didn't appear then the filter will fail. 

`fit-in-width-height width height`

To optimize run time one can choose to check only catalysts bounded by some rectangle. That means if all catalysts centers fit inside rectangle of size (width, height) only then the validation would be made. 

The main usage of this optimization is to allow many catalysts in small rectangle that run in pretty large search space. 

`full-report <file name>`

If you want to report all valid catalysts ignoring the filters into seperate file.

file name - specify file name for the full-report. 

 NOTE: fit-in-width-height optimization will still run and ignore all that don't fit. The difference is that fit-in optimization is pre-calcuation filter, while the usual filter is post-calculation filter, so the report can still be done into seperate place. 
 
 If you don't specify this flag the code will ignore it and report only the results after filter. 
 
 `combine-results yes [<survive-0> <survive-1> <survive-2>]`
 
 If this feature is enabled the search will at first ignore all filters and survival inputs, and will search all the posible catalysts. Then it will try to combine all the found catalysts in all possible combinations, and only then will filter by max survive and apply the filters to exclude them from the final report. 
 
 This feature will generate report as follows: 
 
 `output.rle - all the possible catalysts.     `     
 `output.rle_Combined*.rle - will generate all combined reports.    `     
 `output.rle_Final.rle - the final report. **This is the main output.**    `    
 
 Optional survival filter per "combine iteration" are added. Combine works as follows: each time it start from the initial search results (combine by default uses survive count = 1), and tries to add catalyst from those results. Sometimes one could get explosion, if the interaction is very potent. So filter is added to limit the combine, by surviving coung (if something doesn't survive with two catalyst for 5 iterations, it's probably junk - so CatForce will filter it on the second combine iteration and not in the end). 
 
 This allows faster and more efficient combine operation with very potent conduits which otherwise would overflow the system, with many useless catalysts. 
 
 **NOTE** Recomended for use only for num-catalyst = 1/2 more than that not recommended. 
 
 **NOTE** See 4.in file for example. 
 
 **NOTE** CatForce will use the last survive-x as the default from that point on. If you don't enter any numbers it will use survival count 1, and will filter only when finish all possible combinations.
 
  Compilation
--

Compiled using g++. 
Compile the CatForce as any c++ application with single file. 
Just make sure you have the latest LifeAPI.h (or the one in the repository) in the same folder. 

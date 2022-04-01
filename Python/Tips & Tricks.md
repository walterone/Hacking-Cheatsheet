## Debugger (PDB)
#pdb
#debugger

```python
import pdb; pdb.set_trace()
```



#### s(tep)

Execute the current line, stop at the first possible occasion (either in a function that is called or on the next line in the current function).

#### n(ext)

Continue execution until the next line in the current function is reached or it returns. (The difference between [next](https://docs.python.org/3/library/pdb.html#pdbcommand-next) and [step](https://docs.python.org/3/library/pdb.html#pdbcommand-step) is that [step](https://docs.python.org/3/library/pdb.html#pdbcommand-step) stops inside a called function, while next executes called functions at (nearly) full speed, only stopping at the next line in the current function.)

#### p expression

Evaluate the expression in the current context and print its value.
 <br>
*E molto di piu' nella [Documentazione](https://docs.python.org/3/library/pdb.html)]*






## Virtualenv (Python2 & pip2)
#python2

Soluzione virtualenv da forum offsec
https://forums.offensive-security.com/showthread.php?42031-For-those-with-problems-with-python3


> [This guide served well to me](https://computingforgeeks.com/how-to-install-python2-with-virtualenv-on-ubuntu/)  <br>
In a nutshell: 
`apt install virtualenv`
`virtualenv --python=python2 directory_name`  
go to that directory_name and: `source bin/activate`  
Once this python2 environment has been set, you can pip install whatever you want only for that environment, let say you want to: pip install impacket  
Now, move your python scripts into the root of this folder and run them as usual. When done, simply enter command: deactivate
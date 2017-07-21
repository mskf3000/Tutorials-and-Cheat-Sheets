## NodeJS - Uninstalling NodeJS and NPM in Mac OS X

By Jack Szwergold

To uninstall NodeJS and NPM from Mac OS X run the following command:

    sudo rm -rf /usr/local/{lib/node{,/.npm,_modules},bin,share/man}/{npm*,node*,man1/node*}

Then you might need to run these commands to get rid of remaining stuff:

    sudo rm -rf /usr/local/lib/{node,node_modules}
    sudo rm -rf /usr/local/include/{node,node_modules}
    sudo rm -rf ~/{.npm,.node_repl_history}

***

*NodeJS - Uninstalling NodeJS and NPM in Mac OS X (c) by Jack Szwergold; written on February 3, 2015. This work is licensed under a Creative Commons Attribution-NonCommercial 4.0 International License (CC-BY-NC-4.0).*
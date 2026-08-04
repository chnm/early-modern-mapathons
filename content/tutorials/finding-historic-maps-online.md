---
title: "Finding Historic Maps Online Tutorial"
date: "2026-04-12"
publishDate: "2026-04-12"
author: "Katherine Kania"
weight: 4
aliases:
  - "/2026/04/12/finding-historic-maps-online-tutorial/"
---

**Where can I find historic maps for my project?**

Unless you can physically be in an archive and have permission to digitize a map yourself, the best place to find early modern maps is online. There are many online databases that contain digitized historic maps that you can download for free and are either in the public domain or may be used under creative commons license with attribution.*

*Remember, it is your responsibility to verify that the source you are using is not under copyright, or to obtain permission to use the copyrighted material!

  1. **David Rumsey Historical Map Collection**

[**https://www.davidrumsey.com/**](https://www.davidrumsey.com/)****

The David Rumsey Map Collection is an online database run by Stanford University. It contains over 142,000 digitized maps from around the world, ranging in date from 1550 to the present. You can search their database through the standard search bar at the top of the page, search by category, or use their ‘search by text-on-maps’ feature which locates maps by a keyword search of the text they contain (though this function is currently only available for maps that have already been georeferenced in their database).

![](/images/tutorials/Screenshot-2025-10-21-172835.png)

  2. **Old Maps Online**

[**https://www.oldmapsonline.org/**](https://www.oldmapsonline.org/)****

Old Maps Online is a digital repository where you can search by zooming into a location on their world map projection and using the timeline slider to narrow down the search results to a specific year or era. Old Maps Online searches through multiple institutions at one time, so you’ll see an aggregate of maps from across the web in your search results.

![](/images/tutorials/Screenshot-2025-10-21-173801.png)

  3. **The Huntington Digital Library**

[**https://hdl.huntington.org/digital/collection/p15150coll4/search**](https://hdl.huntington.org/digital/collection/p15150coll4/search)****

The Huntington Digital Library has a collection of digitized world maps from 1695 to 1968. You can find digitized maps on the Huntington Digital Library in a few easy steps:

First, in the left-hand sidebar, find the ‘Collections’ drop-down box and click on ‘Maps.’ From there, you can filter results through other metadata like ‘Creator,’ ‘Date,’ ‘Contributor,’ ‘Language,’ and ‘Subjects.’

![](/images/tutorials/Screenshot-2025-12-12-170246.png)

While many of the maps in the Huntington’s collection can be used through Creative Commons licenses, _it is your responsibility as the researcher to determine if there is a copyright on a digitized source you want to use, and to obtain permission to use it._

  4. **National Libraries or National Archives**

Another option for finding digitized maps online is to go straight to official national or regional websites for the country you are researching. Some examples include:

[U.S. Library of Congress](https://www.loc.gov/)

[The British Library](https://www.bl.uk/)

[National Library of Mexico](https://bnm.iib.unam.mx/)

[National Library of France](https://www.bnf.fr/en/bibliotheque-nationale-de-france-catalogue-general)

[National Library of Scotland](https://www.nls.uk/)

In some cases, a country will have regional government archives. For example, Italy has specific websites for various regions such as Veneto, the region of northeastern Italy home to the cities of Venice, Modena, Verona, etc. 

* * *

**Types of Historic Maps: How do I determine if a map is right for my project?**

As you search for an early modern map online, you will encounter many different styles of maps. This is because map projections were not standardized in the early modern period, and cartographers had free reign over artistic style. This means that when you search through digitized maps, you need to look for specific attributes that make the map a good candidate for creating shapefiles.

**Example 1: 1716 Map of Venice, Italy**

![](/images/tutorials/Screenshot-2025-11-10-141754.png)

“Venetia.” https://www.davidrumsey.com/luna/servlet/detail/RUMSEY~8~1~305895~90076280:Venetia-.

Elements that make this 1716 map good for creating GIS shapefiles.

  * Aerial view 
    * This map has an aerial projection. The geographic layout is  _flat_ and 2D.
  * Scan Quality 
    * A good quality scan is important to create shapefiles. Fuzzy/grainy scans make it difficult to distinguish elements on the map. Sometimes though, it’s not the fault of the scanner – some maps are too damaged by time, wear-and-tear, or poor preservation to create quality scans.
  * Well-defined Shapes 
    * This 1716 map has a clear grid, and well-defined lines that make is easy to create shapefiles on top of. Notice that the buildings are not 3D. Instead they are represented by 2D shapes that have clear lines and closed boundaries.

**Example 2: 1575 Map of Venice**

![](/images/tutorials/Screenshot-2025-11-10-142015.png)

“Vol I (43) Venetia (Venice).” https://www.davidrumsey.com/luna/servlet/detail/RUMSEY~8~1~286822~90059073:Vol-I–43–Venetia–Venice–.

Why does this map not work for creating GIS shapefiles?

While this 1575 map meets one of the requirements (good scan quality), and partially fulfills another (aerial view), it is **not** a good candidate for creating shapefiles. This is because the map has an **axonometric projection**. Essentially, the axonometric projection tilts a 3D drawing to make it appear to be in 2D. You can see more than one side of the buildings, but**it is not entirely 3D or 2D**. So, while the general shape/outline of a 2D map of Venice is shown, the buildings, canals, and ships appear tilted at an angle. This type of projection was popular in early modern mapmaking, but does not help the modern historian create **geographically accurate shapefiles**.

Axonometric projections are artistically and stylistically interesting, but don’t work for creating shapefiles because of the lack of defined 2D shapes which decreases the accuracy of pinpointing the correct locations of elements on a modern map.

* * *

**What do I do if I can’t find an early modern map for my project?**

Sometimes it is not possible to locate an appropriate, well-preserved, and well-digitized historic map. In those cases, don’t give up! There are a few options for the map-less early modern historian. 

  1. **19****th****Century Travel Guides**

In the 19th century, travel guides were a popular way to learn about places around the world. Often, people would document their experiences and the sites and cities they saw on their journeys, which were then printed for public consumption (think of it like an old-school travel blog). Many 19th century travel guides have been digitized and can be found through various online primary source databases that can be accessed through your university library or a personal account. 

  2. **Official Tourism City Guides**

Modern city guides can be found on many official websites for tourism boards and state/regional/city governments. City guides offer highlights for tourists, which almost always includes historic buildings, churches, palaces, parks, etc. Take the city of Modena, Italy for example which has [ _La Guida di Modena, a_](https://laguidadimodena.it/en/home) n online guide to the city of Modena that lists historic sites, their descriptions, and their locations.

After you have compiled a list of the historic buildings, streets, rivers, churches, homes, parks/gardens, etc. and have identified that they were built in the era you are researching, you can locate them on a modern map (the address or directions to specific historic sites are often listed with their descriptions), and create shapefiles for them on the modern map, without using a historic map. 

This method is effective, but time consuming, and you should be aware that your map may not be complete when you have finished your research. This is okay. The point is to compile the most accurate information possible so you can create the best representation of the early modern city that you are studying in the absence of a digitized historic map. As you continue your research, you may find additional information and are always able to add more shapefiles to your GIS project.

* * *

**Other Resources/Further Reading:**

[Cornell Library Guide – Digital Map Libraries](https://guides.library.cornell.edu/historicmapsonline)

[History of Cartography](https://www.geographyrealm.com/mapping-through-the-ages/)

[Types of Medieval European Maps](https://www.geographyrealm.com/types-medieval-european-maps/)

[Folger Library – Mapping Early Modern Worlds](https://folgerpedia.folger.edu/Mapping_Early_Modern_Worlds)

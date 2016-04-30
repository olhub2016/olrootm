# olrootm
The {overlayfs,unlonfs} exploiting root merge bash scripts for the {BSD,linux} dists .

#A.

 This bash script suit is bound to build and sustain the system  
   subdivided into two parts ( BASE+OVERLAY ) in the terms of time and(or) packages  
   and put in two different partitions .  

 The first part ( BASE ROOT ) functions independently .  
   
 The second one ( OVERLAY ROOT ) is placed at the OVERLAY ROOT PARTITION  
  ( immediately or inside its own dir )  
  and merged under the choice through overlayfs or unionfs-fuse during the session .

 The install or upgrade of the kernel is to be ALWAYS fullfilled from the BASE ROOT system session .

 The scripts are potentially dist independent .
 
 The initial setup of the OVERLAY ROOT is fullfilled 
  by the Overlay_Build_Merge_UMerge Script ( option "Build" ) .

#B.

 There are two opportunities for the session in the merged system as to Xorg :

  1. One can start new Xorg instance from the other linux tty .

   From the BASE system session , copy Startx+xinitrc into the /Over_root dir of the OVERLAY ROOT  
     
       $ sudo cp  /PATH_TO/StartX  /PATH_TO/OVERLAY_ROOT/Over_root
     
       $  sudo cp /PATH_TO/xinitrc /PATH_TO/OVERLAY_ROOT/Over_root/.xinitrc

   Having merged AND chrooted in the other linux tty shell , start new Xorg with the command :
   
    \#  sh /root/StartX

  2. In the already presented Xorg , 
     one can start new window for the merged ( but not chrooted ) system 
     by Xephyr_chroot script .

#C.

 The session in the merged system , despite nominally of the root user kind ,
  doesn't alter in any way the BASE ROOT
  and leaves all the changes in the OVERLAY ROOT PARTITION .

 The scripts "Make_Base...Overlay_SFS_Snapshot" create Squashfs ball out of the pkgs+home+root carring dirs
  of the BASE(OVERLAY) ROOT
  and the "SFS_Base+Overlay...Overlay_Merge_UMerge" scripts  merge OVERLAY SFS ball
  with the BASE ROOT SFS ball or BASE ROOT correspondingly by unionfs-fuse
  although with the somehow restricted functioning .
  Only the maiden /root and /tmp directories preserved in the RAM /dev/shm/Mount_SFS_Overlay dir
  are writable in this case .
 
#D.

 When some OVERLAY ROOT is set in the OVERLAY PARTITION immediately ( without any enclosing dir ) ,
  one can BOOT into the system merged with ANY OVERLAY ROOT .
  
  In Archlinux , being within BASE System ,

   1. Make BASE_Mount dir within OVERLAY ROOT PARTITION

        $ sudo mkdir /PATH_TO/OVERLAY_ROOT/Base_Mount

   2. Make proper initramfs .

      Edit initcpio_hooks_overlay_root file in respect of the partitions layout and OVERLAY ROOT you want to merge with .
      
         $ nano /PATH_TO/Overlay_Hooks/initcpio_hooks_overlay_root
       
      $ sudo cp /PATH_TO/Overlay_Hooks/initcpio_install_overlay_root /usr/lib/initcpio/install/overlay_root
       
      $ sudo cp /PATH_TO/Overlay_Hooks/sinitcpio_hooks_overlay_root /usr/lib/initcpio/hooks/overlay_root
       
      $ sudo cp /etc/mkinitcpio.conf /etc/mkinitcpio-overlay-fallback.conf
       
      Edit "MODULES=" line in the mkinitcpio-overlay-fallback.conf file : add "overlay" .
       
      Edit "HOOKS=" line in the mkinitcpio-overlay-fallback.conf file : add "overlay_root" , remove "autodetect" .
       
         $ sudo nano  /etc/mkinitcpio-overlay-fallback.conf
       
      $ sudo mkinitcpio -c /etc/mkinitcpio-overlay-fallback.conf -g /boot/initramfs-linux-overlay-fallback.img
       
     To regenerate initramfs-linux-overlay-fallback.img every time upgrading linux kernel
     edit /etc/mkinitcpio.d/linux.preset file like that :
     
        ## Change PRESETS line
      
          PRESETS=('default' 'fallback' 'overlay')
       
        ## Add these lines to the file
       
          #overlay_config="/etc/mkinitcpio.conf"
       
           overlay_image="/boot/initramfs-linux-overlay-fallback.img"
       
           overlay_options="-A overlay_root -S autodetect"

   3. Set up new items in the bootloader conf file
       pointing at the initramfs-linux-overlay-fallback.img and OVERLAY ROOT PARTITION as a root .

   4. Repeat p. 2,3 for every OVERLAY ROOT renaming and editing files coherently .

   5. Edit overlay_etc_fstab file and copy it in the OVERLAY ROOT PARTITION and OVERLAY ROOTs . 
     
        $ nano /PATH_TO/overlay_etc_fstab
     
        $ sudo cp /PATH_TO/overlay_etc_fstab  /PATH_TO/OVERLAY_ROOT_PARTITION/etc/fstab  
        $ sudo cp /PATH_TO/overlay_etc_fstab  /PATH_TO/OVERLAY_ROOT_PARTITION/OVERLAY_ROOT/OVER_etc/fstab

   6. Reboot .

 


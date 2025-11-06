# maya_arkit52_bake

Prereqs (before Maya)
In UE5.6, bake the MetaHuman face Animation Sequence to the Face Control Rig (Face_ControlBoard_CtrlRig).
Export an FBX Animation (skeletal) OR just use the Control Rig keys via MetaHuman for Maya.
In Maya, assemble the same MetaHuman via MetaHuman for Maya (DNA paths valid). Timeline at 24 fps. Frames: neutral at 0, ARKit poses on 1–51 (we omit mouthClose; tongueOut is at 52 in our default).

Install/load the tool (Maya Python tab)
import importlib, sys
sys.path.append(r"D:/DXG/CG/Maya/Plugins/MH_mh_arkit_mapping_anim_To_BlendShape")
import maya_arkit52_bake as ab
importlib.reload(ab)

Simple use (head-only)
ab.quick_run()

Simple use (multi-mesh)
Select ALL face meshes you want shapes on (head + teeth + saliva + eyes + eyelashes + eye shell + eye edge + cartilage):
ab.quick_run_multi()

Make the head’s ARKit BS drive the others (select head first, then all other meshes):
ab.wire_selected_to_first_bs()

Before FBX export (bake keys per mesh so FBX preserves animation)
Key the head driver across the pose sequence (neutral@0, ARKit@1–50 and 52; mouthClose omitted). If you haven’t keyed the head BS yet, ask and I’ll paste the exact snippet for your scene.
Select ONLY target meshes (not head), then bake weights from head → targets, and enforce deformer order:
ab.bake_weights_to_targets(driver_mesh="head_lod0_mesh", start_frame=0, end_frame=52)
ab.enforce_deformer_order(mesh_list=[
  "head_lod0_mesh","teeth_lod0_mesh","saliva_lod0_mesh",
  "eyeLeft_lod0_mesh","eyeRight_lod0_mesh","eyeshell_lod0_mesh",
  "eyelashes_lod0_mesh","eyeEdge_lod0_mesh","cartilage_lod0_mesh"
])
FBX export: Deformed Models ON, Skins ON, Blend Shapes ON, Animation ON, Bake Animation 24 fps. Export the joints that actually influence each mesh. Keep mesh transforms identity (t=0 r=0 s=1) and no scaled parents

Notes
Layout: duplicates are arranged on XY with 16:9, spacing X=15, Y=40, group offset (-300, 100, 0). BlendShape origin is local, so grid placement won’t pull the base head.
Mapping: neutral at frame 0; ARKit names map to frames 1–51 (mouthClose omitted; tongueOut covered).
If a wrong ARKit node ends up on the wrong mesh, delete it, select that mesh alone, run ab.quick_run_multi() to rebuild, then rewire with ab.wire_selected_to_first_bs().
Minimal troubleshooting
Eyelashes/eyes/teeth don’t move after FBX: per-mesh keys are missing. Re-run bake to targets (select targets only).
Teeth at origin after FBX: ensure identity TRS, unscaled parents, correct deformer order, the jaw/tongue joints were exported, and Bake Animation is enabled. If constraints exist, bake them to keys first.

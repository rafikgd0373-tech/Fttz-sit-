# Fttz Website - Scripts Only

## Home.tsx
```typescript
import { useState, useEffect } from "react";
import { useTheme } from "@/contexts/ThemeContext";
import { useLanguage } from "@/contexts/LanguageContext";
import { useAuth } from "@/_core/hooks/useAuth";
import { getLoginUrl } from "@/const";
import { trpc } from "@/lib/trpc";
import { toast } from "sonner";
import { useLocation } from "wouter";
import {
  Menu, X, Moon, Sun, Globe, LogIn, LogOut, User,
  Heart, Youtube, Twitch, MessageCircle, ExternalLink,
  Star, Trash2, PlusCircle, BookOpen, ChevronRight,
  Settings, Film
} from "lucide-react";
import { Button } from "@/components/ui/button";
import { Dialog, DialogContent, DialogHeader, DialogTitle } from "@/components/ui/dialog";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Label } from "@/components/ui/label";
import { Avatar, AvatarFallback, AvatarImage } from "@/components/ui/avatar";

const PARTICLES = Array.from({ length: 14 }, (_, i) => ({
  id: i,
  x: Math.random() * 100,
  y: Math.random() * 100,
  dur: 2.5 + Math.random() * 3,
  delay: Math.random() * 3,
  size: 3 + Math.random() * 5,
}));

const PROFILE_IMG = "https://d2xsxph8kpxj0f.cloudfront.net/310519663394727871/ZuoJQjwarx58gm6KMAZt2x/39894_9caacd27.png";

export default function Home() {
  const { theme, toggleTheme } = useTheme();
  const safeToggleTheme = () => toggleTheme && toggleTheme();
  const { lang, setLang, t, isRTL } = useLanguage();
  const { user, isAuthenticated, logout } = useAuth();
  const safeLogout = () => logout();
  const [, navigate] = useLocation();

  const [sidebarOpen, setSidebarOpen] = useState(false);
  const [activePanel, setActivePanel] = useState<"menu" | "settings" | "profile" | null>(null);
  const [editName, setEditName] = useState("");
  const [editBio, setEditBio] = useState("");
  const [avatarFile, setAvatarFile] = useState<File | null>(null);
  const [avatarPreview, setAvatarPreview] = useState<string | null>(null);
  const [saving, setSaving] = useState(false);

  const updateProfile = trpc.user.updateProfile.useMutation();
  const uploadAvatar = trpc.user.uploadAvatar.useMutation();

  useEffect(() => {
    if (user) {
      setEditName(user.name || "");
      setEditBio((user as any).bio || "");
    }
  }, [user]);

  const handleAvatarChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files?.[0];
    if (!file) return;
    setAvatarFile(file);
    const reader = new FileReader();
    reader.onload = ev => setAvatarPreview(ev.target?.result as string);
    reader.readAsDataURL(file);
  };

  const handleSaveProfile = async () => {
    setSaving(true);
    try {
      if (avatarFile && avatarPreview) {
        const base64 = avatarPreview.split(",")[1];
        await uploadAvatar.mutateAsync({ base64, mimeType: avatarFile.type });
      }
      await updateProfile.mutateAsync({ name: editName || undefined, bio: editBio || undefined });
      toast.success(t.saved);
    } catch {
      toast.error("Error saving profile");
    } finally {
      setSaving(false);
    }
  };

  const openPanel = (panel: "settings" | "profile") => {
    setActivePanel(panel);
    setSidebarOpen(false);
  };

  const closeSidebar = () => {
    setSidebarOpen(false);
    setActivePanel(null);
  };

  const profileData = [
    { label: t.age, value: "17" },
    { label: t.name, value: "كايزر / رفيق" },
    { label: t.fav_food, value: "اندومي 🍜 و برجر 🍔" },
    { label: t.fav_drink, value: "حمود بوعلام 🥤" },
    { label: t.country, value: "الجزائر 🇩🇿" },
    { label: t.fav_characters, value: "ايزاغي • كايزر • باتشيرا" },
    { label: t.fav_anime, value: "بلو لوك ⚽" },
  ];

  const isDark = theme === "dark";

  return (
    <div className={`min-h-screen ${isDark ? "bg-[oklch(8%_0.01_260)]" : "bg-[oklch(98%_0_0)]"} text-foreground`} dir={isRTL ? "rtl" : "ltr"}>
      <nav className={`fixed top-0 left-0 right-0 z-50 flex items-center justify-between px-4 py-3 ${isDark ? "bg-[oklch(10%_0.02_260)/90]" : "bg-white/90"} backdrop-blur-md border-b border-[oklch(87%_0.28_130/0.2)]`}>
        <span className="font-['Orbitron',sans-serif] font-bold text-xl neon-text tracking-widest">FTTZ</span>
        <div className={`flex items-center gap-3 ${isRTL ? "flex-row-reverse" : ""}`}>
          <button onClick={safeToggleTheme} className="p-2 rounded-full hover:bg-primary/10 transition-colors text-foreground">
            {isDark ? <Sun size={18} className="text-yellow-400" /> : <Moon size={18} className="text-slate-600" />}
          </button>
          <button onClick={() => setSidebarOpen(true)} className="p-2 rounded-full hover:bg-primary/10 transition-colors text-foreground">
            <Menu size={22} />
          </button>
        </div>
      </nav>

      <section className="relative min-h-screen flex flex-col items-center justify-center overflow-hidden pt-16">
        <div className="absolute inset-0 bg-gradient-to-b from-[oklch(15%_0.08_150)] via-[oklch(10%_0.05_200)] to-[oklch(8%_0.01_260)]" />
        <div className="absolute inset-0 opacity-30" style={{background: "conic-gradient(from 0deg at 50% 45%, transparent 0deg, oklch(30% 0.15 130 / 0.3) 8deg, transparent 16deg)"}} />
        {PARTICLES.map(p => (
          <div key={p.id} className="particle absolute rounded-full bg-[oklch(87%_0.28_130)]" style={{ left: `${p.x}%`, top: `${p.y}%`, width: p.size, height: p.size, "--dur": `${p.dur}s`, "--delay": `${p.delay}s` } as React.CSSProperties} />
        ))}
        <div className="relative z-10 mb-6">
          <div className="w-36 h-36 rounded-full glow-pulse neon-border border-4 overflow-hidden">
            <img src={PROFILE_IMG} alt="Fttz" className="w-full h-full object-cover" />
          </div>
        </div>
        <div className="relative z-10 text-center px-4">
          <h1 className="font-['Orbitron',sans-serif] font-black text-4xl sm:text-5xl md:text-6xl text-white mb-3 drop-shadow-2xl">Wolcom To Fttz page</h1>
          <p className="text-[oklch(87%_0.28_130)] font-['Cairo',sans-serif] text-xl font-bold tracking-wide mb-4">Be Fttz_edit</p>
          <div className="w-24 h-1 bg-[oklch(87%_0.28_130)] mx-auto rounded-full neon-glow-ring" />
        </div>
        <div className="absolute bottom-8 left-1/2 -translate-x-1/2 z-10 flex flex-col items-center gap-1 animate-bounce">
          <div className="w-0.5 h-8 bg-gradient-to-b from-[oklch(87%_0.28_130)] to-transparent" />
          <div className="w-2 h-2 rounded-full bg-[oklch(87%_0.28_130)]" />
        </div>
      </section>

      <section className={`py-16 px-4 ${isDark ? "bg-[oklch(10%_0.02_260)]" : "bg-[oklch(96%_0.01_130)]"}`}>
        <div className="max-w-2xl mx-auto">
          <h2 className="font-['Orbitron',sans-serif] text-2xl font-bold neon-text text-center mb-10 tracking-wider">{t.profile_title}</h2>
          <div className="grid gap-3">
            {profileData.map(({ label, value }) => (
              <div key={label} className={`flex items-center gap-4 p-4 rounded-xl border border-[oklch(87%_0.28_130/0.2)] ${isDark ? "bg-[oklch(12%_0.02_260)]" : "bg-white"} hover:border-[oklch(87%_0.28_130/0.5)] transition-all`}>
                <div className="w-1.5 h-8 rounded-full bg-[oklch(87%_0.28_130)] flex-shrink-0" />
                <div className={`flex flex-1 ${isRTL ? "flex-row-reverse" : ""} items-center justify-between gap-2 flex-wrap`}>
                  <span className={`text-sm font-semibold ${isDark ? "text-[oklch(60%_0_0)]" : "text-[oklch(40%_0_0)]"} font-['Cairo',sans-serif]`}>{label}</span>
                  <span className="font-['Cairo',sans-serif] font-bold text-foreground text-right">{value}</span>
                </div>
              </div>
            ))}
          </div>
        </div>
      </section>

      <section className={`py-12 px-4 text-center ${isDark ? "bg-[oklch(8%_0.01_260)]" : "bg-[oklch(98%_0_0)]"}`}>
        <button onClick={() => navigate("/edits")} className="inline-flex items-center gap-3 px-8 py-4 rounded-2xl bg-[oklch(87%_0.28_130)] text-black font-bold text-lg font-['Orbitron',sans-serif] hover:scale-105 transition-transform neon-glow-ring">
          <Film size={22} /> {t.nav_edits} <ChevronRight size={18} className={isRTL ? "rotate-180" : ""} />
        </button>
      </section>

      <section className={`py-16 px-4 ${isDark ? "bg-[oklch(10%_0.02_260)]" : "bg-[oklch(96%_0.01_130)]"}`}>
        <div className="max-w-2xl mx-auto">
          <h2 className="font-['Orbitron',sans-serif] text-2xl font-bold neon-text text-center mb-10 tracking-wider">{t.social_title}</h2>
          <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
            <a href="https://discord.gg/EDj5qhWJUe" target="_blank" rel="noopener noreferrer" className={`flex items-center gap-4 p-5 rounded-2xl border border-[oklch(87%_0.28_130/0.2)] ${isDark ? "bg-[oklch(12%_0.02_260)]" : "bg-white"} hover:border-[oklch(87%_0.28_130)] hover:scale-105 transition-all group`}>
              <div className="w-12 h-12 rounded-xl bg-[#5865F2] flex items-center justify-center flex-shrink-0"><MessageCircle size={20} className="text-white" /></div>
              <div className="flex-1"><p className="font-bold text-foreground">{t.discord}</p><p className="text-xs text-muted-foreground">discord.gg/EDj5qhWJUe</p></div>
              <ExternalLink size={16} className="text-muted-foreground group-hover:text-foreground transition-colors" />
            </a>
            <a href="https://twitch.tv/algeriamood" target="_blank" rel="noopener noreferrer" className={`flex items-center gap-4 p-5 rounded-2xl border border-[oklch(87%_0.28_130/0.2)] ${isDark ? "bg-[oklch(12%_0.02_260)]" : "bg-white"} hover:border-[oklch(87%_0.28_130)] hover:scale-105 transition-all group`}>
              <div className="w-12 h-12 rounded-xl bg-[#9146FF] flex items-center justify-center flex-shrink-0"><Twitch size={20} className="text-white" /></div>
              <div className="flex-1"><p className="font-bold text-foreground">{t.twitch}</p><p className="text-xs text-muted-foreground">algeriamood</p></div>
              <ExternalLink size={16} className="text-muted-foreground group-hover:text-foreground transition-colors" />
            </a>
            <a href="https://youtube.com/@fttz_edit" target="_blank" rel="noopener noreferrer" className={`flex items-center gap-4 p-5 rounded-2xl border border-[oklch(87%_0.28_130/0.2)] ${isDark ? "bg-[oklch(12%_0.02_260)]" : "bg-white"} hover:border-[oklch(87%_0.28_130)] hover:scale-105 transition-all group`}>
              <div className="w-12 h-12 rounded-xl bg-[#FF0000] flex items-center justify-center flex-shrink-0"><Youtube size={20} className="text-white" /></div>
              <div className="flex-1"><p className="font-bold text-foreground">{t.youtube}</p><p className="text-xs text-muted-foreground">@fttz_edit</p></div>
              <ExternalLink size={16} className="text-muted-foreground group-hover:text-foreground transition-colors" />
            </a>
            <a href="https://fttzpage-zuojqjwa.manus.space" target="_blank" rel="noopener noreferrer" className={`flex items-center gap-4 p-5 rounded-2xl border border-[oklch(87%_0.28_130/0.2)] ${isDark ? "bg-[oklch(12%_0.02_260)]" : "bg-white"} hover:border-[oklch(87%_0.28_130)] hover:scale-105 transition-all group`}>
              <div className="w-12 h-12 rounded-xl bg-[oklch(87%_0.28_130)] flex items-center justify-center flex-shrink-0"><Globe size={20} className="text-black" /></div>
              <div className="flex-1"><p className="font-bold text-foreground">{t.website}</p><p className="text-xs text-muted-foreground">Fttz - page</p></div>
              <ExternalLink size={16} className="text-muted-foreground group-hover:text-foreground transition-colors" />
            </a>
          </div>
        </div>
      </section>

      {sidebarOpen && <div className="fixed inset-0 bg-black/50 z-40" onClick={closeSidebar} />}
      <div className={`fixed top-0 ${isRTL ? "right-0" : "left-0"} h-screen w-80 bg-background border-l border-border z-50 transform transition-transform ${sidebarOpen ? "translate-x-0" : (isRTL ? "translate-x-full" : "-translate-x-full")}`}>
        <div className="p-4 border-b border-border flex items-center justify-between">
          <h3 className="font-bold text-lg">{t.nav_edits}</h3>
          <button onClick={closeSidebar} className="p-2 hover:bg-primary/10 rounded-full"><X size={20} /></button>
        </div>
        <div className="p-4 space-y-2">
          {activePanel === null && (
            <>
              <button onClick={() => { navigate("/edits"); closeSidebar(); }} className="w-full flex items-center gap-3 p-3 rounded-lg hover:bg-primary/10 transition-colors text-left">
                <Film size={18} /> {t.nav_edits}
              </button>
              <button onClick={() => openPanel("settings")} className="w-full flex items-center gap-3 p-3 rounded-lg hover:bg-primary/10 transition-colors text-left">
                <Settings size={18} /> {t.nav_settings}
              </button>
              {!isAuthenticated ? (
                <a href={getLoginUrl()} className="w-full flex items-center gap-3 p-3 rounded-lg hover:bg-primary/10 transition-colors text-left">
                  <LogIn size={18} /> {t.nav_login}
                </a>
              ) : (
                <button onClick={() => openPanel("profile")} className="w-full flex items-center gap-3 p-3 rounded-lg hover:bg-primary/10 transition-colors text-left">
                  <User size={18} /> {t.nav_profile}
                </button>
              )}
            </>
          )}
          {activePanel === "settings" && (
            <>
              <button onClick={() => setActivePanel(null)} className="w-full flex items-center gap-2 p-2 text-sm text-muted-foreground hover:text-foreground mb-4">
                ← {t.close}
              </button>
              <div className="space-y-4">
                <div>
                  <p className="text-sm font-semibold mb-2">{t.dark_mode}</p>
                  <button onClick={safeToggleTheme} className="w-full flex items-center justify-between p-3 rounded-lg bg-primary/10">
                    {isDark ? <Sun size={18} /> : <Moon size={18} />}
                    <span className="text-sm">{isDark ? t.light_mode : t.dark_mode}</span>
                  </button>
                </div>
                <div>
                  <p className="text-sm font-semibold mb-2">{t.language}</p>
                  <div className="flex gap-2">
                    <button onClick={() => setLang("en")} className={`flex-1 p-2 rounded-lg text-sm font-semibold transition-colors ${lang === "en" ? "bg-primary text-primary-foreground" : "bg-primary/10"}`}>EN</button>
                    <button onClick={() => setLang("ar")} className={`flex-1 p-2 rounded-lg text-sm font-semibold transition-colors ${lang === "ar" ? "bg-primary text-primary-foreground" : "bg-primary/10"}`}>AR</button>
                  </div>
                </div>
              </div>
            </>
          )}
          {activePanel === "profile" && isAuthenticated && (
            <>
              <button onClick={() => setActivePanel(null)} className="w-full flex items-center gap-2 p-2 text-sm text-muted-foreground hover:text-foreground mb-4">
                ← {t.close}
              </button>
              <div className="space-y-4">
                <div>
                  <Label>{t.avatar}</Label>
                  <input type="file" accept="image/*" onChange={handleAvatarChange} className="w-full mt-2" />
                  {avatarPreview && <img src={avatarPreview} alt="preview" className="w-20 h-20 rounded-lg mt-2 object-cover" />}
                </div>
                <div>
                  <Label>{t.display_name}</Label>
                  <Input value={editName} onChange={e => setEditName(e.target.value)} className="mt-2" />
                </div>
                <div>
                  <Label>{t.bio}</Label>
                  <Textarea value={editBio} onChange={e => setEditBio(e.target.value)} className="mt-2" />
                </div>
                <Button onClick={handleSaveProfile} disabled={saving} className="w-full">
                  {saving ? t.saving : t.save}
                </Button>
                <Button onClick={safeLogout} variant="outline" className="w-full">
                  {t.nav_logout}
                </Button>
              </div>
            </>
          )}
        </div>
      </div>
    </div>
  );
}
```

## EditsPage.tsx
```typescript
import { useState, useRef } from "react";
import { useTheme } from "@/contexts/ThemeContext";
import { useLanguage } from "@/contexts/LanguageContext";
import { useAuth } from "@/_core/hooks/useAuth";
import { getLoginUrl } from "@/const";
import { trpc } from "@/lib/trpc";
import { toast } from "sonner";
import { useLocation } from "wouter";
import {
  Heart, Trash2, Star, BookOpen, BookMarked,
  PlusCircle, X, ArrowLeft, Upload, Film,
  ChevronLeft, ChevronRight
} from "lucide-react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Dialog, DialogContent, DialogHeader, DialogTitle } from "@/components/ui/dialog";
import { useLanguage as useLang } from "@/contexts/LanguageContext";

function StarRating({ value, onChange, readonly }: { value: number; onChange?: (v: number) => void; readonly?: boolean }) {
  const [hover, setHover] = useState(0);
  return (
    <div className="flex gap-0.5">
      {[1, 2, 3, 4, 5].map(star => (
        <button key={star} type="button" disabled={readonly} onClick={() => onChange?.(star)} onMouseEnter={() => !readonly && setHover(star)} onMouseLeave={() => !readonly && setHover(0)} className={`transition-colors ${readonly ? "cursor-default" : "cursor-pointer"}`}>
          <Star size={18} className={`transition-colors ${(hover || value) >= star ? "fill-[oklch(87%_0.28_130)] text-[oklch(87%_0.28_130)]" : "text-muted-foreground"}`} />
        </button>
      ))}
    </div>
  );
}

function VideoCard({ video, isAdmin, onDelete, onRate, onToggleMuseum, onLike }: {
  video: { id: number; title: string; videoUrl: string; rating: number | null; inMuseum: boolean; likeCount: number; liked: boolean; };
  isAdmin: boolean;
  onDelete: (id: number) => void;
  onRate: (id: number, rating: number) => void;
  onToggleMuseum: (id: number, inMuseum: boolean) => void;
  onLike: (id: number) => void;
}) {
  const { t, isRTL } = useLang();
  const { theme } = useTheme();
  const isDark = theme === "dark";
  const videoRef = useRef<HTMLVideoElement>(null);
  const [showControls, setShowControls] = useState(false);
  const [confirmDelete, setConfirmDelete] = useState(false);
  const [localLiked, setLocalLiked] = useState(video.liked);
  const [localLikeCount, setLocalLikeCount] = useState(video.likeCount);
  const { isAuthenticated } = useAuth();

  const handleLike = () => {
    if (!isAuthenticated) { toast.info(isRTL ? "سجّل دخولك أولاً" : "Login to like videos"); return; }
    setLocalLiked(l => !l);
    setLocalLikeCount(c => localLiked ? c - 1 : c + 1);
    onLike(video.id);
  };

  return (
    <div className="shorts-item relative flex items-center justify-center bg-black">
      <video ref={videoRef} src={video.videoUrl} className="h-full w-full object-contain" loop playsInline onClick={() => videoRef.current?.paused ? videoRef.current.play() : videoRef.current?.pause()} />
      <div className="absolute inset-0 bg-gradient-to-t from-black/80 via-transparent to-black/20 pointer-events-none" />
      {video.inMuseum && (
        <div className="absolute top-4 left-4 flex items-center gap-1.5 px-3 py-1 rounded-full bg-[oklch(87%_0.28_130/0.2)] border border-[oklch(87%_0.28_130/0.5)] backdrop-blur-sm">
          <BookMarked size={12} className="text-[oklch(87%_0.28_130)]" />
          <span className="text-xs font-semibold text-[oklch(87%_0.28_130)] font-['Cairo',sans-serif]">{t.in_museum}</span>
        </div>
      )}
      {(video.rating ?? 0) > 0 && (
        <div className="absolute top-4 right-4 flex items-center gap-1 px-2 py-1 rounded-full bg-black/50 backdrop-blur-sm">
          <Star size={12} className="fill-[oklch(87%_0.28_130)] text-[oklch(87%_0.28_130)]" />
          <span className="text-xs font-bold text-white">{(video.rating ?? 0).toFixed(1)}</span>
        </div>
      )}
      <div className="absolute bottom-0 left-0 right-0 p-4 bg-gradient-to-t from-black/90 to-transparent">
        <p className="text-white font-bold mb-2 font-['Cairo',sans-serif]">{video.title}</p>
        <div className="flex items-center justify-between gap-2">
          <button onClick={handleLike} className="flex items-center gap-1 px-3 py-1 rounded-full bg-white/10 hover:bg-white/20 transition-colors">
            <Heart size={16} className={localLiked ? "fill-red-500 text-red-500" : "text-white"} />
            <span className="text-xs text-white">{localLikeCount}</span>
          </button>
          {isAdmin && (
            <div className="flex gap-1">
              <button onClick={() => setShowControls(!showControls)} className="p-1.5 rounded-full bg-white/10 hover:bg-white/20 transition-colors">
                <Star size={14} className="text-white" />
              </button>
            </div>
          )}
        </div>
      </div>
      {showControls && isAdmin && (
        <div className="absolute inset-0 bg-black/70 flex flex-col items-center justify-center gap-3 p-4">
          <StarRating value={video.rating ?? 0} onChange={r => onRate(video.id, r)} />
          <button onClick={() => onToggleMuseum(video.id, !video.inMuseum)} className="px-3 py-1 rounded-lg bg-[oklch(87%_0.28_130)] text-black text-sm font-bold">
            {video.inMuseum ? t.museum_remove : t.museum_add}
          </button>
          <button onClick={() => setConfirmDelete(true)} className="px-3 py-1 rounded-lg bg-red-500 text-white text-sm font-bold">
            {t.delete_video}
          </button>
        </div>
      )}
      {confirmDelete && (
        <div className="absolute inset-0 bg-black/90 flex flex-col items-center justify-center gap-3 p-4">
          <p className="text-white text-center">{t.confirm_delete}</p>
          <div className="flex gap-2">
            <button onClick={() => { onDelete(video.id); setConfirmDelete(false); }} className="px-4 py-2 rounded-lg bg-red-500 text-white font-bold">
              {t.yes_delete}
            </button>
            <button onClick={() => setConfirmDelete(false)} className="px-4 py-2 rounded-lg bg-white/20 text-white font-bold">
              {t.cancel}
            </button>
          </div>
        </div>
      )}
    </div>
  );
}

export default function EditsPage() {
  const { theme } = useTheme();
  const { t, isRTL } = useLanguage();
  const { user, isAuthenticated } = useAuth();
  const [, navigate] = useLocation();
  const isDark = theme === "dark";
  const isAdmin = user?.role === "admin";

  const [uploadOpen, setUploadOpen] = useState(false);
  const [uploadTitle, setUploadTitle] = useState("");
  const [uploadFile, setUploadFile] = useState<File | null>(null);
  const [uploading, setUploading] = useState(false);
  const [currentIndex, setCurrentIndex] = useState(0);

  const videosQuery = trpc.videos.list.useQuery();
  const uploadMutation = trpc.videos.upload.useMutation();
  const deleteMutation = trpc.videos.delete.useMutation();
  const rateMutation = trpc.videos.rate.useMutation();
  const museumMutation = trpc.videos.toggleMuseum.useMutation();
  const likeMutation = trpc.videos.like.useMutation();

  const videos = videosQuery.data || [];

  const handleUpload = async () => {
    if (!uploadTitle || !uploadFile) return;
    setUploading(true);
    try {
      const reader = new FileReader();
      reader.onload = async (e) => {
        const base64 = (e.target?.result as string).split(",")[1];
        await uploadMutation.mutateAsync({ title: uploadTitle, base64, mimeType: uploadFile.type });
        setUploadTitle("");
        setUploadFile(null);
        setUploadOpen(false);
        await videosQuery.refetch();
        toast.success(t.upload_btn);
      };
      reader.readAsDataURL(uploadFile);
    } finally {
      setUploading(false);
    }
  };

  const handleDelete = async (id: number) => {
    await deleteMutation.mutateAsync({ id });
    await videosQuery.refetch();
    toast.success(t.delete_video);
  };

  const handleRate = async (id: number, rating: number) => {
    await rateMutation.mutateAsync({ id, rating });
    await videosQuery.refetch();
  };

  const handleToggleMuseum = async (id: number, inMuseum: boolean) => {
    await museumMutation.mutateAsync({ id, inMuseum });
    await videosQuery.refetch();
  };

  const handleLike = async (id: number) => {
    await likeMutation.mutateAsync({ id });
    await videosQuery.refetch();
  };

  return (
    <div className={`min-h-screen ${isDark ? "bg-[oklch(8%_0.01_260)]" : "bg-[oklch(98%_0_0)]"}`}>
      <div className="flex items-center justify-between p-4 border-b border-border">
        <button onClick={() => navigate("/")} className="flex items-center gap-2 text-foreground hover:text-primary">
          <ArrowLeft size={20} /> {t.close}
        </button>
        <h1 className="font-['Orbitron',sans-serif] font-bold text-xl neon-text">{t.videos_title}</h1>
        {isAdmin && (
          <button onClick={() => setUploadOpen(true)} className="p-2 rounded-full bg-[oklch(87%_0.28_130)] text-black hover:scale-110 transition-transform">
            <PlusCircle size={24} />
          </button>
        )}
      </div>

      {videos.length === 0 ? (
        <div className="h-screen flex items-center justify-center text-center">
          <p className="text-muted-foreground">{t.no_videos}</p>
        </div>
      ) : (
        <div className="shorts-container">
          {videos.map((video, idx) => (
            <VideoCard key={video.id} video={video} isAdmin={isAdmin} onDelete={handleDelete} onRate={handleRate} onToggleMuseum={handleToggleMuseum} onLike={handleLike} />
          ))}
        </div>
      )}

      <Dialog open={uploadOpen} onOpenChange={setUploadOpen}>
        <DialogContent>
          <DialogHeader>
            <DialogTitle>{t.upload_video}</DialogTitle>
          </DialogHeader>
          <div className="space-y-4">
            <div>
              <Label>{t.video_title_label}</Label>
              <Input value={uploadTitle} onChange={e => setUploadTitle(e.target.value)} placeholder="Video title" className="mt-2" />
            </div>
            <div>
              <Label>{t.choose_file}</Label>
              <input type="file" accept="video/*" onChange={e => setUploadFile(e.target.files?.[0] || null)} className="w-full mt-2" />
            </div>
            <Button onClick={handleUpload} disabled={uploading || !uploadTitle || !uploadFile} className="w-full">
              {uploading ? t.uploading : t.upload_btn}
            </Button>
          </div>
        </DialogContent>
      </Dialog>
    </div>
  );
}
```

## Database Schema (schema.ts)
```typescript
import { boolean, int, mysqlEnum, mysqlTable, text, timestamp, varchar, float } from "drizzle-orm/mysql-core";

export const users = mysqlTable("users", {
  id: int("id").autoincrement().primaryKey(),
  openId: varchar("openId", { length: 64 }).notNull().unique(),
  name: text("name"),
  email: varchar("email", { length: 320 }),
  loginMethod: varchar("loginMethod", { length: 64 }),
  role: mysqlEnum("role", ["user", "admin"]).default("user").notNull(),
  bio: text("bio"),
  avatarUrl: text("avatarUrl"),
  createdAt: timestamp("createdAt").defaultNow().notNull(),
  updatedAt: timestamp("updatedAt").defaultNow().onUpdateNow().notNull(),
  lastSignedIn: timestamp("lastSignedIn").defaultNow().notNull(),
});

export type User = typeof users.$inferSelect;
export type InsertUser = typeof users.$inferInsert;

export const videos = mysqlTable("videos", {
  id: int("id").autoincrement().primaryKey(),
  title: text("title").notNull(),
  videoUrl: text("videoUrl").notNull(),
  videoKey: text("videoKey").notNull(),
  thumbnailUrl: text("thumbnailUrl"),
  rating: float("rating").default(0),
  inMuseum: boolean("inMuseum").default(false).notNull(),
  uploadedBy: int("uploadedBy").notNull(),
  createdAt: timestamp("createdAt").defaultNow().notNull(),
  updatedAt: timestamp("updatedAt").defaultNow().onUpdateNow().notNull(),
});

export type Video = typeof videos.$inferSelect;
export type InsertVideo = typeof videos.$inferInsert;

export const videoLikes = mysqlTable("video_likes", {
  id: int("id").autoincrement().primaryKey(),
  videoId: int("videoId").notNull(),
  userId: int("userId").notNull(),
  createdAt: timestamp("createdAt").defaultNow().notNull(),
});

export type VideoLike = typeof videoLikes.$inferSelect;
```

## Server Routes (routers.ts)
```typescript
import { z } from "zod";
import { COOKIE_NAME } from "@shared/const";
import { getSessionCookieOptions } from "./_core/cookies";
import { systemRouter } from "./_core/systemRouter";
import { protectedProcedure, publicProcedure, router } from "./_core/trpc";
import { createVideo, deleteVideo, getLikesForVideos, listVideos, toggleLike, updateUserProfile, updateVideo } from "./db";
import { storagePut } from "./storage";
import { nanoid } from "nanoid";

const adminProcedure = protectedProcedure.use(({ ctx, next }) => {
  if (ctx.user.role !== "admin") throw new TRPCError({ code: "FORBIDDEN", message: "Admin only" });
  return next({ ctx });
});

export const appRouter = router({
  system: systemRouter,
  auth: router({
    me: publicProcedure.query(opts => opts.ctx.user),
    logout: publicProcedure.mutation(({ ctx }) => {
      const cookieOptions = getSessionCookieOptions(ctx.req);
      ctx.res.clearCookie(COOKIE_NAME, { ...cookieOptions, maxAge: -1 });
      return { success: true } as const;
    }),
  }),
  user: router({
    updateProfile: protectedProcedure.input(z.object({ name: z.string().min(1).max(100).optional(), bio: z.string().max(500).optional(), avatarUrl: z.string().url().optional() })).mutation(async ({ ctx, input }) => {
      await updateUserProfile(ctx.user.id, input);
      return { success: true };
    }),
    uploadAvatar: protectedProcedure.input(z.object({ base64: z.string(), mimeType: z.string() })).mutation(async ({ ctx, input }) => {
      const buffer = Buffer.from(input.base64, "base64");
      const ext = input.mimeType.split("/")[1] || "jpg";
      const key = `avatars/${ctx.user.id}-${nanoid(8)}.${ext}`;
      const { url } = await storagePut(key, buffer, input.mimeType);
      await updateUserProfile(ctx.user.id, { avatarUrl: url });
      return { url };
    }),
  }),
  videos: router({
    list: publicProcedure.query(async ({ ctx }) => {
      const vids = await listVideos();
      const ids = vids.map(v => v.id);
      const likesMap = await getLikesForVideos(ids, ctx.user?.id);
      return vids.map(v => ({ ...v, likeCount: likesMap[v.id]?.count ?? 0, liked: likesMap[v.id]?.liked ?? false }));
    }),
    upload: adminProcedure.input(z.object({ title: z.string().min(1).max(200), base64: z.string(), mimeType: z.string() })).mutation(async ({ ctx, input }) => {
      const buffer = Buffer.from(input.base64, "base64");
      const ext = input.mimeType.split("/")[1] || "mp4";
      const key = `videos/${ctx.user.id}-${nanoid(10)}.${ext}`;
      const { url } = await storagePut(key, buffer, input.mimeType);
      const video = await createVideo({ title: input.title, videoUrl: url, videoKey: key, uploadedBy: ctx.user.id });
      return video;
    }),
    rate: adminProcedure.input(z.object({ id: z.number(), rating: z.number().min(0).max(5) })).mutation(async ({ input }) => {
      await updateVideo(input.id, { rating: input.rating });
      return { success: true };
    }),
    toggleMuseum: adminProcedure.input(z.object({ id: z.number(), inMuseum: z.boolean() })).mutation(async ({ input }) => {
      await updateVideo(input.id, { inMuseum: input.inMuseum });
      return { success: true };
    }),
    delete: adminProcedure.input(z.object({ id: z.number() })).mutation(async ({ input }) => {
      await deleteVideo(input.id);
      return { success: true };
    }),
    like: protectedProcedure.input(z.object({ id: z.number() })).mutation(async ({ ctx, input }) => {
      const liked = await toggleLike(input.id, ctx.user.id);
      return { liked };
    }),
  }),
});

export type AppRouter = typeof appRouter;
```

## Language Context (LanguageContext.tsx)
```typescript
import React, { createContext, useContext, useEffect, useState } from "react";

export type Language = "en" | "ar";

interface Translations {
  nav_edits: string;
  nav_settings: string;
  nav_login: string;
  nav_logout: string;
  nav_profile: string;
  welcome: string;
  profile_title: string;
  age: string;
  name: string;
  fav_food: string;
  fav_drink: string;
  country: string;
  fav_characters: string;
  fav_anime: string;
  social_title: string;
  discord: string;
  twitch: string;
  youtube: string;
  website: string;
  videos_title: string;
  upload_video: string;
  video_title_label: string;
  choose_file: string;
  upload_btn: string;
  uploading: string;
  delete_video: string;
  rate_video: string;
  museum_add: string;
  museum_remove: string;
  like: string;
  no_videos: string;
  settings_title: string;
  dark_mode: string;
  light_mode: string;
  language: string;
  edit_profile: string;
  display_name: string;
  bio: string;
  avatar: string;
  save: string;
  saving: string;
  saved: string;
  login_title: string;
  login_btn: string;
  museum_title: string;
  in_museum: string;
  close: string;
  cancel: string;
  confirm_delete: string;
  yes_delete: string;
}

const en: Translations = {
  nav_edits: "Edits",
  nav_settings: "Settings",
  nav_login: "Login",
  nav_logout: "Logout",
  nav_profile: "Profile",
  welcome: "Welcome to Fttz",
  profile_title: "About Me",
  age: "Age",
  name: "Name",
  fav_food: "Favorite Food",
  fav_drink: "Favorite Drink",
  country: "Country",
  fav_characters: "Favorite Characters",
  fav_anime: "Favorite Anime",
  social_title: "Find Me Online",
  discord: "Discord Server",
  twitch: "Twitch",
  youtube: "YouTube",
  website: "Website",
  videos_title: "Edits",
  upload_video: "Upload Video",
  video_title_label: "Video Title",
  choose_file: "Choose Video File",
  upload_btn: "Upload",
  uploading: "Uploading...",
  delete_video: "Delete",
  rate_video: "Rate",
  museum_add: "Add to Museum",
  museum_remove: "Remove from Museum",
  like: "Like",
  no_videos: "No videos yet. Check back soon!",
  settings_title: "Settings",
  dark_mode: "Dark Mode",
  light_mode: "Light Mode",
  language: "Language",
  edit_profile: "Edit Profile",
  display_name: "Display Name",
  bio: "Bio",
  avatar: "Profile Picture",
  save: "Save",
  saving: "Saving...",
  saved: "Saved!",
  login_title: "Login / Register",
  login_btn: "Login with Manus",
  museum_title: "Museum",
  in_museum: "In Museum",
  close: "Close",
  cancel: "Cancel",
  confirm_delete: "Are you sure you want to delete this video?",
  yes_delete: "Yes, Delete",
};

const ar: Translations = {
  nav_edits: "الإيديتات",
  nav_settings: "الإعدادات",
  nav_login: "تسجيل الدخول",
  nav_logout: "تسجيل الخروج",
  nav_profile: "الملف الشخصي",
  welcome: "أهلاً بك في Fttz",
  profile_title: "عني",
  age: "العمر",
  name: "الاسم",
  fav_food: "الأكل المفضل",
  fav_drink: "المشروب المفضل",
  country: "البلد",
  fav_characters: "الشخصية المفضلة",
  fav_anime: "الأنمي المفضل",
  social_title: "تواصل معي",
  discord: "سيرفر ديسكورد",
  twitch: "تويتش",
  youtube: "يوتيوب",
  website: "الموقع",
  videos_title: "الإيديتات",
  upload_video: "رفع فيديو",
  video_title_label: "عنوان الفيديو",
  choose_file: "اختر ملف الفيديو",
  upload_btn: "رفع",
  uploading: "جاري الرفع...",
  delete_video: "حذف",
  rate_video: "تقييم",
  museum_add: "أضف للمتحف",
  museum_remove: "أزل من المتحف",
  like: "إعجاب",
  no_videos: "لا توجد فيديوهات بعد. تابعنا قريباً!",
  settings_title: "الإعدادات",
  dark_mode: "الوضع الداكن",
  light_mode: "الوضع الفاتح",
  language: "اللغة",
  edit_profile: "تعديل الملف الشخصي",
  display_name: "الاسم المعروض",
  bio: "نبذة عني",
  avatar: "صورة الملف الشخصي",
  save: "حفظ",
  saving: "جاري الحفظ...",
  saved: "تم الحفظ!",
  login_title: "تسجيل الدخول / إنشاء حساب",
  login_btn: "الدخول عبر Manus",
  museum_title: "المتحف",
  in_museum: "في المتحف",
  close: "إغلاق",
  cancel: "إلغاء",
  confirm_delete: "هل أنت متأكد من حذف هذا الفيديو؟",
  yes_delete: "نعم، احذف",
};

interface LanguageContextType {
  lang: Language;
  setLang: (l: Language) => void;
  t: Translations;
  isRTL: boolean;
}

const LanguageContext = createContext<LanguageContextType>({
  lang: "ar",
  setLang: () => {},
  t: ar,
  isRTL: true,
});

export function LanguageProvider({ children }: { children: React.ReactNode }) {
  const [lang, setLangState] = useState<Language>(() => {
    return (localStorage.getItem("fttz-lang") as Language) || "ar";
  });

  const setLang = (l: Language) => {
    setLangState(l);
    localStorage.setItem("fttz-lang", l);
  };

  const isRTL = lang === "ar";

  useEffect(() => {
    document.documentElement.setAttribute("dir", isRTL ? "rtl" : "ltr");
    document.documentElement.setAttribute("lang", lang);
  }, [lang, isRTL]);

  const t = lang === "ar" ? ar : en;

  return (
    <LanguageContext.Provider value={{ lang, setLang, t, isRTL }}>
      {children}
    </LanguageContext.Provider>
  );
}

export function useLanguage() {
  return useContext(LanguageContext);
}
```

## Global CSS (index.css)
```css
@import "tw-animate-css";
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Cairo:wght@400;600;700;900&family=Inter:wght@400;500;600;700&display=swap');

@custom-variant dark (&:is(.dark *));

@theme inline {
  --radius-sm: calc(var(--radius) - 4px);
  --radius-md: calc(var(--radius) - 2px);
  --radius-lg: var(--radius);
  --radius-xl: calc(var(--radius) + 4px);
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-card: var(--card);
  --color-card-foreground: var(--card-foreground);
  --color-popover: var(--popover);
  --color-popover-foreground: var(--popover-foreground);
  --color-primary: var(--primary);
  --color-primary-foreground: var(--primary-foreground);
  --color-secondary: var(--secondary);
  --color-secondary-foreground: var(--secondary-foreground);
  --color-muted: var(--muted);
  --color-muted-foreground: var(--muted-foreground);
  --color-accent: var(--accent);
  --color-accent-foreground: var(--accent-foreground);
  --color-destructive: var(--destructive);
  --color-destructive-foreground: var(--destructive-foreground);
  --color-border: var(--border);
  --color-input: var(--input);
  --color-ring: var(--ring);
}

:root {
  --neon: oklch(87% 0.28 130);
  --neon-dim: oklch(70% 0.22 130);
  --neon-glow: oklch(87% 0.28 130 / 0.4);
  --radius: 0.75rem;
  --primary: oklch(87% 0.28 130);
  --primary-foreground: oklch(10% 0 0);
  --background: oklch(98% 0 0);
  --foreground: oklch(10% 0 0);
  --card: oklch(95% 0 0);
  --card-foreground: oklch(10% 0 0);
  --popover: oklch(98% 0 0);
  --popover-foreground: oklch(10% 0 0);
  --secondary: oklch(92% 0.02 130);
  --secondary-foreground: oklch(20% 0 0);
  --muted: oklch(93% 0 0);
  --muted-foreground: oklch(50% 0 0);
  --accent: oklch(87% 0.28 130);
  --accent-foreground: oklch(10% 0 0);
  --destructive: oklch(60% 0.22 25);
  --destructive-foreground: oklch(98% 0 0);
  --border: oklch(88% 0 0);
  --input: oklch(88% 0 0);
  --ring: oklch(87% 0.28 130);
}

.dark {
  --primary: oklch(87% 0.28 130);
  --primary-foreground: oklch(8% 0 0);
  --background: oklch(8% 0.01 260);
  --foreground: oklch(95% 0 0);
  --card: oklch(12% 0.02 260);
  --card-foreground: oklch(95% 0 0);
  --popover: oklch(10% 0.02 260);
  --popover-foreground: oklch(95% 0 0);
  --secondary: oklch(18% 0.03 260);
  --secondary-foreground: oklch(90% 0 0);
  --muted: oklch(16% 0.02 260);
  --muted-foreground: oklch(60% 0 0);
  --accent: oklch(87% 0.28 130);
  --accent-foreground: oklch(8% 0 0);
  --destructive: oklch(55% 0.22 25);
  --destructive-foreground: oklch(98% 0 0);
  --border: oklch(16% 0.02 260);
  --input: oklch(14% 0.02 260);
  --ring: oklch(87% 0.28 130);
}

@layer base {
  * { @apply border-border; }
  body { @apply bg-background text-foreground; }
  h1, h2, h3, h4, h5, h6 { @apply scroll-m-20 font-bold; }
  button { @apply transition-colors; }
}

.neon-text {
  color: var(--neon);
  text-shadow: 0 0 10px var(--neon-glow);
}

.neon-border {
  border-color: var(--neon);
  box-shadow: 0 0 20px var(--neon-glow), inset 0 0 20px var(--neon-glow);
}

.neon-glow-ring {
  box-shadow: 0 0 20px var(--neon-glow);
}

.glow-pulse {
  animation: glow-pulse 2s ease-in-out infinite;
}

@keyframes glow-pulse {
  0%, 100% { box-shadow: 0 0 20px var(--neon-glow), inset 0 0 20px var(--neon-glow); }
  50% { box-shadow: 0 0 40px var(--neon-glow), inset 0 0 40px var(--neon-glow); }
}

.particle {
  animation: float var(--dur) ease-in-out var(--delay) infinite;
}

@keyframes float {
  0%, 100% { transform: translateY(0) translateX(0); opacity: 0.5; }
  50% { transform: translateY(-20px) translateX(10px); opacity: 1; }
}

.shorts-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 1rem;
  padding: 1rem;
  max-height: calc(100vh - 80px);
  overflow-y: auto;
}

.shorts-item {
  aspect-ratio: 9/16;
  border-radius: 1rem;
  overflow: hidden;
  cursor: pointer;
}

@media (max-width: 768px) {
  .shorts-container {
    grid-template-columns: 1fr;
  }
}

[dir="rtl"] {
  direction: rtl;
  text-align: right;
}

[dir="ltr"] {
  direction: ltr;
  text-align: left;
}
```

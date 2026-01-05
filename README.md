# 🚀 Guide de Lancement LOCADZ

Ton application est prête ! Voici comment la mettre entre les mains de tes utilisateurs.

## 1. Configurer la base de données (Supabase)
1. Crée un projet sur [Supabase](https://supabase.com/).
2. Ouvre l'**SQL Editor** dans ton tableau de bord Supabase.
3. Copie et exécute ce script pour créer toutes les tables :

```sql
-- Table des Utilisateurs
CREATE TABLE IF NOT EXISTS public.users (
    id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
    full_name text,
    email text UNIQUE,
    phone_number text,
    avatar_url text,
    role text DEFAULT 'TRAVELER',
    id_verification_status text DEFAULT 'NONE',
    is_verified boolean DEFAULT false,
    is_phone_verified boolean DEFAULT false,
    id_document_url text,
    payout_details jsonb DEFAULT '{"method": "NONE", "accountName": "", "accountNumber": ""}'::jsonb,
    created_at timestamptz DEFAULT now()
);

-- Table des Propriétés
CREATE TABLE IF NOT EXISTS public.properties (
    id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
    host_id uuid REFERENCES public.users(id),
    title text,
    description text,
    location text,
    price numeric,
    category text,
    rating numeric DEFAULT 5.0,
    reviews_count int DEFAULT 0,
    latitude numeric,
    longitude numeric,
    created_at timestamptz DEFAULT now()
);

-- Table des Images
CREATE TABLE IF NOT EXISTS public.property_images (
    id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
    property_id uuid REFERENCES public.properties(id) ON DELETE CASCADE,
    image_url text,
    created_at timestamptz DEFAULT now()
);

-- Table des Réservations
CREATE TABLE IF NOT EXISTS public.bookings (
    id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
    property_id uuid REFERENCES public.properties(id),
    traveler_id uuid REFERENCES public.users(id),
    start_date date,
    end_date date,
    total_price numeric,
    commission_fee numeric,
    status text DEFAULT 'PENDING_APPROVAL',
    payment_method text,
    payment_id text,
    receipt_url text,
    created_at timestamptz DEFAULT now()
);

-- Table des Avis
CREATE TABLE IF NOT EXISTS public.reviews (
    id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
    property_id uuid REFERENCES public.properties(id),
    user_id uuid REFERENCES public.users(id),
    user_name text,
    user_avatar text,
    rating int,
    comment text,
    created_at timestamptz DEFAULT now()
);

-- Table des Favoris
CREATE TABLE IF NOT EXISTS public.favorites (
    id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
    traveler_id uuid REFERENCES public.users(id),
    property_id uuid REFERENCES public.properties(id),
    created_at timestamptz DEFAULT now()
);

-- Sécurité RLS (Autoriser tout pour le test, à restreindre plus tard)
ALTER TABLE public.users ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Allow All" ON public.users FOR ALL USING (true) WITH CHECK (true);
ALTER TABLE public.properties ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Allow All" ON public.properties FOR ALL USING (true) WITH CHECK (true);
ALTER TABLE public.property_images ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Allow All" ON public.property_images FOR ALL USING (true) WITH CHECK (true);
ALTER TABLE public.bookings ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Allow All" ON public.bookings FOR ALL USING (true) WITH CHECK (true);
ALTER TABLE public.reviews ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Allow All" ON public.reviews FOR ALL USING (true) WITH CHECK (true);
ALTER TABLE public.favorites ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Allow All" ON public.favorites FOR ALL USING (true) WITH CHECK (true);
```

## 2. Déployer sur le Web
1. Mets ton code sur **GitHub**.
2. Connecte-toi sur [Vercel](https://vercel.com/).
3. Clique sur **"Add New" > "Project"** et choisis ton dépôt.
4. **CRUCIAL** : Dans l'onglet "Environment Variables", ajoute :
   - **Key** : `API_KEY`
   - **Value** : (Ta clé API Gemini de Google AI Studio)
5. Clique sur **Deploy**.

## 3. Utiliser sur ton téléphone
Une fois ton site en ligne (ex: `locadz.vercel.app`) :
1. Ouvre le lien sur ton téléphone.
2. **Sur iPhone** : Appuie sur l'icône de partage (le petit carré avec une flèche) et sélectionne **"Sur l'écran d'accueil"**.
3. **Sur Android** : Appuie sur les trois points en haut à droite et sélectionne **"Installer l'application"**.

Voilà ! Tu as maintenant une vraie icône "LOCADZ" sur ton téléphone. 🚀